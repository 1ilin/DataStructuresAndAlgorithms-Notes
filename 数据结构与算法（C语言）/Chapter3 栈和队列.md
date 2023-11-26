> 栈和队列是两种特殊的线性表
## 重点
(1)栈、队列的定义、特点、性质和应用； 
(2)栈、队列的设计和实现以及基本操作及相关算法。
## 难点
(1)循环队列； 
(2)栈、队列的应用。

# 栈
---
## 定义
栈(Stack)是一种特殊的线性表，其插入和删除操作均在表的一端进行，是一种运算受限的线性表。
栈顶(top)是栈中允许插入和删除的一端。 
栈底(bottom)是栈顶的另一端。
![[Pasted image 20230910154326.png]]

### 抽象数据类型
![[Pasted image 20230910154443.png]]

## 栈的表示和实现
### 类型定义
```c
//----- 回顾线性表的实现 ----- 
#define LIST_INIT_SIZE 80 // 线性表存储空间的初始分配量 
#define LISTINCREMENT 10 // 线性表存储空间的分配增量
typedef struct {  
	ElemType *elem; // 存储空间基址 
	int length; // 当前长度 
	int listsize; // 当前分配的存储容量(以sizeof(ElemType)为单位)
} SqList; // 俗称顺序表

//----- 栈的顺序存储表示 ----- 
#define STACK_INIT_SIZE 100; //栈容量 
#define STACKINCREMENT 10; //栈增量 
typedef struct { 
	SElemType *base; //基地址 
	SElemType *top; //栈顶指针 
	int stacksize; //栈容量 
} SqStack; 
SqStack S;
```
### 初始化
![[Pasted image 20230910154907.png]]
```c
Status InitStack (SqStack &S){ // 构造一个空栈S 
	S.base=(SElemType*)malloc(STACK_INIT_SIZE*sizeof(SElemType)); 
	if (!S.base) 
		exit (OVERFLOW); //存储分配失败 
	S.top = S.base; 
	S.stacksize = STACK_INIT_SIZE; 
	return OK; 
}
```
## 栈的基本操作
```c
InitStack(&S) //初始化栈 
DestroyStack(&S) //销毁栈 
ClearStack(&S) //清空栈 
StackEmpty(S) //判栈空 
StackLength(S) //求栈长度 
GetTop(S, &e) //取栈顶元素 
Push(&S, e) //入栈 
Pop(&S, &e) //出栈 
StackTravers(S, visit()) //遍历栈 
```
### 入栈
![[Pasted image 20230910155021.png]]
![[Pasted image 20230910155029.png]]
```c
Status Push (SqStack &S, SElemType e) { 
	if (S.top - S.base >= S.stacksize) { 
		S.base = (ElemType *) realloc ( S.base, (S.stacksize + STACKINCREMENT) * sizeof (ElemType)); 
		if (!S.base) 
			exit (OVERFLOW); //存储分配失败 
		S.top = S.base + S.stacksize; 
		S.stacksize += STACKINCREMENT; 
	} 
	*S.top++ = e; 
	return OK; 
}
```
### 出栈
![[Pasted image 20230910155058.png]]
```c
Status Pop (SqStack &S, SElemType &e) { 
	if (S.top ==S.base) 
		return error; 
	else {
		e=*(S.top-1); 
		S.top--;
	} // 也可`e = *(--S.top);`
	return OK; 
}
```
# 链栈
---
## 链栈的表示与实现
链栈的实现与链表的实现基本相同，头结点作为栈顶位置。
### 类型定义
```c
Typedef struct SNode { 
	SElemType data; //数据域 
	struct Snode *next; //链域 
}SNode, *LinkStack;
```
### 初始化
```c
LinkStack S;  
void InitStack(LinkStack &S){ 
	S=(SNode *) malloc (sizeof (SNode)); 
	if (!S) 
		exit (OVERFLOW); //存储分配失败 
	S->next=NULL; 
}
```
## 基本操作
```c
InitStack(&S) //初始化栈 
DestroyStack(&S) //销毁栈 
ClearStack(&S) //清空栈 
StackEmpty(S) //判栈空 
StackLength(S) //求栈长度 
GetTop(S, &e) //取栈顶元素 
Push(&S, e) //入栈 
Pop(&S, &e) //出栈 
StackTravers(S, visit()) //遍历栈
```
### 入栈
![](Pasted%20image%2020231126150929.png)
```c
void Push(LinkStack &S, SElemType e){
	p=(SNode *) malloc (sizeof (SNode)); 
	p->data=e; 
	p->next=S->next; 
	S->next=p;  
}
```
链栈的入栈操作与头插法建立单链表的方式一致。也就是说，每次将新元素插入到链表的头部，同时更新栈顶指针和链表头指针。这样可以保证栈顶元素始终是链表的第一个元素，符合栈的先进后出的特性。

### 出栈
![](Pasted%20image%2020231126151412.png)
```c
void Pop(LinkStack &S, SElemType &e){
	if (S->next==NULL) 
		return error; 
	p=S->next; 
	e=p->data; 
	S->next=p->next; 
	free(p); 
}
```
链栈的出栈操作等于删除单链表的头部的首元节点。也就是说，如果链栈的栈顶元素是链表的第一个元素，那么出栈操作就相当于删除这个元素，同时更新栈顶指针和链表头指针。这样可以避免遍历链表的耗时操作，提高出栈的效率。

# 栈的应用举例
---
## 进制转换
### 思想
![[Pasted image 20230910155601.png]]
![[Pasted image 20230910155609.png]]
### 算法
```c
// 10进制数N转换成8进制的算法
void conversion () { 
	InitStack(S); 
	scanf ("%d",N); 
	while (N) { 
		Push(S, N % 8); N = N/8; 
	} 
	while (!StackEmpty(S)) { 
		Pop(S,e); 
		printf ( "%d", e ); 
	} 
} // conversion
```

## 括号匹配
![[Pasted image 20230910155725.png]]
### 思想
(1)当遇到左括号时，进栈，遇到右括号时出栈； 
(2) 当遇到某一个右括号时，栈已空，说明到目前为止，右括号多于左括号； 
(3) 从栈中弹出的左括号与当前检验的右括号类型不同，说明出现了括号交叉情况； 
(4) 算术表达式输入完毕，但栈中还有没有匹配的左括号，说明左括号多于右括号。

### 算法
```c
// 括号匹配检验算法
Status check( ) { 
	char ch; 
	InitStack(S); 
	while ((ch=getchar())!='#') { 
		switch (ch) { 
			case (ch=='('||ch=='['||ch=='{'):
				Push(S,ch);
				break; 
			case (ch== ')'): 
				if (StackEmpty(S)) 
					retrun FALSE; 
				else {
					Pop(S,e);
					if(e!= '(') return FALSE;
				} 
				break;
			case (ch== ']'): 
				if (StackEmpty(S)) 
					retrun FALSE; 
				else {
					Pop(S,e);
					if(e!= '[') return FALSE;
				} 
				break;
			………… 
			default:
				break; 
		} 
	} 
	if (StackEmpty(S)) 
		return TRUE; 
	else 
		return FALSE; 
}
```

## 迷宫求解（找通路）
![[Pasted image 20230910155846.png]]
### 思想
(1) 入口位置入栈，作标记以免重复进入。 
(2) 从栈顶位置任选一个可以进的位置，并将此位置作标记避免重复，新进位置入栈。 
(3) 重复(2)，若栈顶位置无路可走，则退栈，从新的栈顶重复(2)。 
(4) 直到找到出口，从栈底到栈顶即是路径，或者栈空，表示从入口到出口没通路。

![[Pasted image 20230910155948.png]]

![[Pasted image 20230910155906.png]]

### 算法
![[Pasted image 20230910160011.png]]

## 表达式求值
![[Pasted image 20230910160135.png]]
![[Pasted image 20230910160146.png]]
### 思想
设置两个栈，一个存操作数，栈名为OPND，一个存操作符，栈名为OPTR栈。 
(1) 首先置操作数栈为空，表达式起始符#为运算符栈的栈底元素； 
(2)依次读入表达式中每个字符，若是操作数则进OPND栈，若是运算符则和OPTR栈的栈顶运算符比较优先权后作相应操作，直到整个表达式操作完毕。
	(1)若栈顶运算符小于输入运算符，输入运算符进栈OPTR； 
	(2)若栈顶运算符等于输入运算符（只有栈顶是 “（”，输入是“）”，或者栈顶是“#”，输入是 “#）”两种情况，分别去除一对括号，或结束。 
	(3)若栈顶运算符大于输入运算符，弹出栈顶运算符，从OPND中弹出两个操作数与弹出运算符计算后再存入OPND栈，继续。

## 两个栈共享数组空间
![](Pasted%20image%2020231126153114.png)
```c
typedef struct { 
	ElemType v[m]; 
	int top[2] ; // 栈顶指针 
}STACK; 
STACK S;

void InitStack(STACK &S) { 
	S.top[0]=-1; 
	S.top[1]=m; 
}

int PUSH(STACK &S,ElemType x,int i) { 
	if ( S.top[1]-S.top[0])==1)//满 
		return error; 
	else switch (i) {
		case 0: 
			S.v[++S.top[0]]=x; 
			break; 
		case 1: 
			S.v[--S.top[1]]=x; 
			break; 
		default: 
			return error; 
	} 
}

int POP(STACK &S,ElemType &x,int i) { 
	if ( i==0 ) 
		if (S.top[0]==-1) 
			return error 
		else {
			x=S.v[S.top[0]]; 
			S.top[0]--;
		} 
	if ( i==1 ) 
		if (S.top[1]==m) 
			return error 
		else {
			x=S.v[S.top[1]]; 
			S.top[0]++;
		} 
}
```

# 队列
---
### 定义
队列(Queue)是一种运算受限的特殊的线性表，它只允许在表的一端进行插入，而在表的另一端进行删除。
队尾(rear)是队列中允许插入的一端。 
队头(front)是队列中允许删除的一端。

队列的特点
	先进先出(First In First Out ，简称FIFO)。 
	又称队列为先进先出表。
![](Pasted%20image%2020231126153701.png)

#### 抽象数据类型
![[Pasted image 20230913164407.png]]

### 队列的基本操作
```c
InitQueue(&Q) //初始化队列 
DestroyQueue(&Q) //销毁队列 
QueueEmpty(Q) //判队列是否空 
QueueLength(Q) //求队列长度 
GetHead(Q, &e) //取队头元素 
ClearQueue(&Q) //清空队列 
EnQueue(&Q, e) //入队列 
DeQueue(&Q, &e) //出队列 
QueueTravers(Q, visit()) //遍历队列
```
## 链队列
```c
// 链队列结点实现
typedef struct QNode { // 结点类型 
	QElemType data; 
	struct QNode *next; 
} QNode, *QueuePtr;

// 链队列数据类型实现
typedef struct { // 链队列类型 
	QueuePtr front; // 队头指针 
	QueuePtr rear; // 队尾指针 
} LinkQueue;
```
链队列常用两个指针的原因是为了方便进行入队和出队的操作。
当有新的元素入队时，只需将新元素插入到尾指针所指的节点后面，并更新尾指针。
当有元素出队时，只需将头指针所指的节点删除，并更新头指针。
这样可以避免遍历链表的开销，提高队列的效率。

### 初始化
![[Pasted image 20230913170554.png]]
```c
// 带头结点的链队列初始化
Status InitQueue (LinkQueue &Q) { // 构造一个空队列Q 
	Q.front = Q.rear = (QueuePtr)malloc(sizeof(QNode)); \
	if (!Q.front) 
		exit (OVERFLOW); //存储分配失败 
	Q.front->next = NULL; 
	return OK; 
}
```
### 入队
![[Pasted image 20230913170630.png]]
```c
Status EnQueue (LinkQueue &Q, QElemType e) { // 插入元素e为Q的新的队尾元素 
	p = (QueuePtr) malloc (sizeof (QNode)); 
	if (!p) 
		exit (OVERFLOW); //存储分配失败 
	p->data = e; 
	p->next = NULL; 
	Q.rear->next = p; 
	Q.rear = p; 
	return OK; 
}
```

### 出队
![[Pasted image 20230913171437.png]]

### 其他基本操作
```c
DestroyQueue(&Q) //销毁队列 
QueueEmpty(Q) //判队列是否空 
QueueLength(Q) //求队列长度 
GetHead(Q, &e) //取队头元素 
ClearQueue(&Q) //清空队列 
QueueTravers(Q, visit()) //遍历队列
```

## 循环队列
### 定义
循环队列是顺序队列的一种特例，它是把顺序队列构造成一个首尾相连的循环表。指针和队列元素之间关系不变。
![](Pasted%20image%2020231126154927.png)

### 顺序队列数据类型实现
```c
#define MAXQSIZE 100 //最大队列长度 
typedef struct { 
	QElemType *base; // 动态分配存储空间 
	int front; // 头指针，若队列不空，指向队列头元素 
	int rear; // 尾指针，若队列不空，指向队列尾元素的下一个位置 
} SqQueue; 
SqQueue Sq;
```

### 对循环队列空/满状态的判别
(1) 另设一个标志区别队列是空还是满；
	例如：设一个变量count用来记录队列中元素个数，当`count==0`时队列为空，当`count==MAXQSIZE`时队列为满。
(2)队满条件：以队头指针在队列尾指针的下一位置 作为队列呈满状态的标志，牺牲一个存储空间；
	队满条件为： `(sq.rear+1) mod maxsize==sq.front`
	队空条件为：`sq.rear==sq.front`

### 队列初始化
```c
Status InitQueue (SqQueue &Q) { // 构造一个空队列Q 
	Q.base = (QElemType *) malloc(MAXQSIZE *sizeof (QElemType)); 
	if (!Q.base) 
		exit (OVERFLOW); // 存储分配失败 
	Q.front = Q.rear = 0; 
	return OK; 
}
```

### 求队列长度
```c
int QueueLength(SqQueue Q) { 
	return (Q.rear-Q.front+MaxSize)%MaxSize; 
}
```

### 入队
![[Pasted image 20230913171816.png]]
```c
Status EnQueue (SqQueue &Q, QElemType e) { // 插入元素e为Q的新的队尾元素 
	if ((Q.rear+1) % MAXQSIZE == Q.front) 
		return ERROR; //队列满 
	Q.base[Q.rear] = e; 
	Q.rear = (Q.rear+1) % MAXQSIZE; 
	return OK; 
}
```

### 出队
![[Pasted image 20230913171823.png]]
```c
Status DeQueue (SqQueue &Q, QElemType &e) { 
	// 若队列不空，则删除Q的队头元素，用e返回其值，并返回OK; 否则返回ERROR 
	if (Q.front == Q.rear) 
		return ERROR; 
	e = Q.base[Q.front]; 
	Q.front = (Q.front+1) % MAXQSIZE; 
	return OK; 
}
```

## 双端队列
### 定义
双端队列是一种特殊的线性表，特殊在限定插入和删除操作只能在表的两端进行
![](Pasted%20image%2020231126155534.png)

### 应用举例
![[Pasted image 20230913173334.png]]
#### 不用双端队列的解决办法
![[Pasted image 20230913173349.png]]

## 队列实现迷宫求解
![[Pasted image 20230913171605.png]]
	广度优先算法

# 本章小结
熟练掌握： 
	(1)栈、队列的定义、特点和性质； 
	(2)ADT栈、ADT队列的设计和实现以及基本操作及相关算法。 
重点学习： 
	ADT栈和队列在表达式求值、括号匹配、数制转换、迷宫求解中的应用，提高利用栈和队列解决实际问题的应用水平

![[Pasted image 20230913173435.png]]
## 小习题
![[Pasted image 20230913173511.png]]
![[Pasted image 20230913173525.png]]
![[Pasted image 20230913173530.png]]