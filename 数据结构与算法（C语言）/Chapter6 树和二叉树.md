# 树
---
## 类型定义
数据对象 D： 
	D是具有相同特性的数据元素的集合。 
数据关系 R：
	若D为空集，则称为空树 。 
	否则: 
		(1) 在D中存在唯一的称为根的数据元素root； 
		(2) 当n>1时，其余结点可分为m (m>0)个互不相交的有限集T1 , T2 , …, Tm，其中每一棵子集本身又是一棵符合本定义的树，称为根root的子树。
## 基本概念
结点: 数据元素+若干指向子树的分支 
	孩子结点、双亲结点 
	兄弟结点、堂兄弟 
	祖先结点、子孙结点 
结点的度: 分支的个数 
树的度: 树中所有结点的度的最大值 
叶子结点: 度为零的结点 
分支结点: 度大于零的结点
(从根到结点的)路径： 由从根到该结点所经分支和结点构成
结点的层次: 假设根结点的层次为1，第l层的结点的子树根结点的层次为l+1
树的深度： 树中叶子结点所在的最大层次
森林： 是m（m≥0）棵互不相交的树的集合
	任何一棵非空树是一个二元组 Tree =（root，F） 其中root 被称为根结点，F 被称为子树森林
![](Pasted%20image%2020231126172823.png)

![[Pasted image 20230927185429.png]]

### 对比树型结构和线性结构的结构特点
![[Pasted image 20230927185527.png]]

## 基本操作
### 查找类
![[Pasted image 20230927184129.png]]
### 插入类
![[Pasted image 20230927184152.png]]
### 删除类
![[Pasted image 20230927184205.png]]

# 二叉树
---
二叉树或为空树，或是由一个根结点加上两棵分别称为左子树和右子树的、互不交的二叉树组成。
![](Pasted%20image%2020231126173044.png)

## 主要基本操作
![[Pasted image 20230927185708.png]]
![[Pasted image 20230927185714.png]]
![[Pasted image 20230927185719.png]]

## 二叉树的重要特性
### 两类特殊二叉树
![[Pasted image 20230927185941.png]]
### 二叉树结构的数字特征
![[Pasted image 20230927185847.png]]
![[Pasted image 20230927185909.png]]
![[Pasted image 20230927185915.png]]

![[Pasted image 20230927185948.png]]
![[Pasted image 20230927185955.png]]

## 二叉树的存储结构
---
### 顺序存储
```c
#define MAX_TREE_SIZE 100 // 二叉树的最大结点数 
typedef TElemType SqBiTree[MAX_ TREE_SIZE]; // 0号单元存储根结点 
SqBiTree bt;
```
![[Pasted image 20230927190918.png]]

对于i号结点，其左孩子的标号为2i，右孩子的标号为2i+1

### 链式存储
#### 二叉链表
![[Pasted image 20230927191102.png]]
```c
typedef struct BiTNode { // 结点结构 
	TElemType data; 
	struct BiTNode *lchild, *rchild; // 左右孩子指针 
} BiTNode, *BiTree;
```
结点结构: `lchild | data | rchild`

#### 三叉链表
![[Pasted image 20230927191150.png]]
```c
typedef struct TriTNode { // 结点结构 
	TElemType data; 
	struct TriTNode *lchild, *rchild; // 左右孩子指针 
	struct TriTNode *parent; //双亲指针 
} TriTNode, *TriTree;
```
结点结构: `parent | lchild | data | rchild`

#### 双亲链表
![[Pasted image 20230927191205.png]]
```c
// 结点结构 
typedef struct BPTNode { 
	TElemType data; 
	int *parent; // 指向双亲的指针 
	char LRTag; // 左、右孩子标志域 
} BPTNode 

// 树结构 
typedef struct BPTree{ 
	BPTNode nodes[MAX_TREE_SIZE]; 
	int num_node; // 结点数目 
	int root; // 根结点的位置 
} BPTree
```

#### 线索链表
##### 线索二叉树
![[Pasted image 20230927193715.png]]
![[Pasted image 20230927193722.png]]
![[Pasted image 20230927193730.png]]
![[Pasted image 20230927193739.png]]
![[Pasted image 20230927193746.png]]

##### 线索链表的类型描述
```c
typedef enum { Link, Thread } PointerThr; // Link==0:指针，Thread==1:线索
typedef struct BiThrNod { 
	TElemType data; 
	struct BiThrNode *lchild, *rchild; // 左右指针 
	PointerThr LTag, RTag; // 左右标志 
} BiThrNode, *BiThrTree;
```

##### 建立线索链表
在中序遍历过程中修改结点的 左、右指针域，以保存当前访问结点的“前驱”和“后继”信息。遍历过程中，附设指针pre和p，p指向当前访问的结点，pre指向p的前驱。 
通过pre和p方便获取当前结点的前驱和后继。然后修改左右子树有为空的域，不空的不改。
![[Pasted image 20230927193904.png]]
```c
void InThreading(BiThrTree p) { 
	if (p) { // 对以p为根的非空二叉树进行线索化 
		InThreading(p->lchild); // 左子树线索化 
		if (!p->lchild) // 建前驱线索 
			{ p->LTag = Thread; p->lchild = pre; } 
		if (!pre->rchild) // 建后继线索 
			{ pre->RTag = Thread; pre->rchild = p; } 
		pre = p; // 保持 pre 指向 p 的前驱 
		InThreading(p->rchild); // 右子树线索化 
	} // if 
} // InThreading
```
![[Pasted image 20230927194110.png]]
![[Pasted image 20230927194116.png]]
![[Pasted image 20230927194124.png]]
![[Pasted image 20230927194136.png]]
加入头结点后的算法
```c
Status InOrderThreading(BiThrTree &Thrt, BiThrTree T) { // 构建中序线索链表 
	if (!(Thrt = (BiThrTree)malloc( sizeof( BiThrNode)))) 
		exit (OVERFLOW); 
	Thrt->LTag = Link; 
	Thrt->RTag =Thread; 
	Thrt->rchild = Thrt; // 右指针回指 
	if (!T) 
		Thrt->lchild = Thrt; 
	else { 
		Thrt->lchild = T; 
		pre = Thrt; 
		InThreading(T); 
		pre->rchild = Thrt; // 处理最后一个结点 
		pre->RTag = Thread; 
		Thrt->rchild = pre; 
	}
	return OK; 
} // InOrderThreading
```

## 二叉树的遍历
---
一、问题的提出 
顺着某一条搜索路径巡访二叉树中的结点，使得每个结点均被访问一次，而且仅被访问一次。 
“访问”的含义可以很广，如：输出结点的信息等。

“遍历”是任何类型均有的操作，对线性结构而言，只有一条搜索路径（因为每个结点均只有一个后继），故不需要另加讨论。而二叉树是非线性结构，每个结点有两个后继，则存在如何遍历即按什么样的搜索路径遍历的问题。
对“二叉树”而言，可以有三条搜索路径： 
§ 1．先上后下的按层次遍历； 
§ 2．先左（子树）后右（子树） 的遍历； 
§ 3．先右（子树）后左（子树） 的遍历。（不讨论）

### 先序遍历
若二叉树为空树，则空操作；否则， 
（1）访问根结点； 
（2）先序遍历左子树； 
（3）先序遍历右子树。

#### 递归算法
```c
void Preorder (BiTree T, void(*visit)(TElemType& e) ) {
	if (T) { 
		visit(T->data); // 访问结点 
		Preorder(T->lchild, visit); // 遍历左子树 
		Preorder(T->rchild, visit);// 遍历右子树 
	} 
}
```
#### 非递归算法（利用栈实现）
```c
void PreOrder( BiTree T) {
	BiTNode *q,*p=T; 
	STACK S;InitStack(S); //递归工作栈 
	if (p!=NULL) PUSH (S,p); 
	while (!IsEmpty(S)) {
		POP(S,q); 
		visit(q); 
		if (q->rchild) PUSH(S,q->rchild ); 
		if (q->lchild) PUSH(S,p->lchild); //进左子树 
	}
}
```

#### 应用：统计叶子结点个数
##### 基本思想
先序(或中序或后序)遍历二叉树，在遍历过程中查找叶子结点，并计数。 
由此，需在遍历算法中增添一个“计数”的参数，并将算法中“访问结点”的操作改为：若是叶子，则计数器增1。
##### 算法
```c
void CountLeaf (BiTree T, int& count){ 
	if ( T ) { 
		if ((!T->lchild)&& (!T->rchild)) 
			count++; // 对叶子结点计数 
		CountLeaf( T->lchild, count); 
		CountLeaf( T->rchild, count); 
	} // if 
} // CountLeaf
```
### 中序遍历
若二叉树为空树，则空操作；否则， 
（1）中序遍历左子树； 
（2）访问根结点； 
（3）中序遍历右子树。

#### 非递归算法
##### a
```c
Status InorderTraverse(BiTree T, Status (*Visit)(TelemType e)){ 
//采用二叉链表存储结构，Visit 是对数据元素操作的应用函数 
//中序遍历二叉树T的非递归算法，对每个数据元素调用函数Visit 
	InitStack(S); 
	Push(S,T); //根指针进栈 
	while(!StackEmpty(S)){ 
		while(GetTop(S,p)&&p) // 确保左孩子不为空
			Push(S,p->lchild); //向左走到尽头 
		Pop(S, p); //空指针退栈 
		if ( !StackEmpty(S )) { //访问节点，向右一步 
			Pop(S, p); 
			if (!Visit(p->data)) return ERROR; 
			Push(S, p->rchild); 
		}//if 
	} // while 
	return OK 
}// InorderTraverse 
```
在遍历左子树的时候，会把空节点也压入栈中，然后再弹出，这是一种多余的操作。下面的代码就没有这样的问题
##### b
```c
Status InorderTraverse(BiTree T, Status (*Visit)(TelemType e)){ 
//采用二叉链表存储结构，Visit 是对数据元素操作的应用函数 
//中序遍历二叉树T的非递归算法，对每个数据元素调用函数Visit 
	InitStack(S); 
	p=T;  // 将 `p` 初始化为根
	while(p||!StackEmpty(S)){ 
		if(p) {Push(S,p); p=p->lchild}; //根指针进栈，遍历左子树 
		else{ //根指针退栈，访问根节点，遍历右子树 
			Pop(S, p); 
			if (!Visit(p->data)) return ERROR; 
			p=p->rchild; 
		}//else 
	} // while 
	return OK 
}// InorderTraverse
```
##### c
```c
BiTNode *GoFarLeft(BiTree T, Stack *S){ 
// 将指针 `T` 移动到最左下的结点，并将经过的节点依次入栈
	if (!T ) return NULL; 
	while (T->lchild ){ 
		Push(S, T); 
		T = T->lchild; 
	} 
	return T; 
}

void Inorder_I(BiTree T, void (*visit) (TelemType& e)){ 
// 通过传递函数指针 `void (*visit) (TelemType& e)` 来访问节点
	Stack *S; 
	t = GoFarLeft(T, S); // 找到最左下的结点 
	while(t) { 
		visit(t->data); 
		if (t->rchild) 
			t = GoFarLeft(t->rchild, S);  // 找到右子树的最左下的结点
		else if ( !StackEmpty(S)) // 栈不空时退栈 
			t = Pop(S); 
			else t = NULL; // 栈空表明遍历结束 
	} // while 
}// Inorder_I
```

##### 实现方式的选择
>sauce:Chatgpt

确定哪个版本更好取决于具体的需求和个人偏好。以下是一些考虑因素：
1. **可读性：**
   - 第一和第二个版本都采用了明确的循环结构，而且在每个步骤中都有清晰的注释。这有助于理解代码的逻辑和目的。第一和第二个版本中的循环条件更加简洁，可能更容易理解。
   - 第三个版本通过调用额外的 `GoFarLeft` 函数来实现遍历的一部分，这可能使得代码的逻辑稍显复杂。然而，这也有助于将代码模块化，并且在某些情况下可能提高代码的可维护性。
2. **栈的管理：**
   - 第一和第二个版本在函数内初始化了栈，这使得函数具有更好的封闭性和独立性。这样的设计更容易重用和理解。
   - 第三个版本要求调用者传递栈指针，这可能会增加调用者使用这个函数时的复杂性。如果栈的管理对于调用者不透明，可能导致错误的使用。
3. **性能：**
   - 从性能的角度来看，这三个版本的时间复杂度都是相同的，因为它们都是通过栈来遍历二叉树的每个节点。性能上的差异可能不太明显。
总体而言，更好的版本可能是那个更容易理解和维护的版本。如果栈管理对调用者透明不是问题，而且代码结构更清晰，那么第一或第二个版本可能更具可读性。如果模块化和代码复用更为重要，那么第三个版本可能更合适。

### 后序遍历
若二叉树为空树，则空操作；否则， 
（1）后序遍历左子树； 
（2）后序遍历右子树； 
（3）访问根结点。 

#### 应用
##### 求二叉树深度
###### 基本思想
从二叉树深度的定义可知，二叉树的深度应为其左、右子树深度的最大值加1。
由此，需先分别求得左、右子树的深度，算法中“访问结点”的操作为：求得左、右子树深度的最大值，然后加 1 。
###### 算法
```c
int Depth (BiTree T ){ // 返回二叉树的深度 
	if ( !T ) depthval = 0; 
	else { 
		depthLeft = Depth( T->lchild ); 
		depthRight= Depth( T->rchild ); 
		depthval = 1 + (depthLeft > depthRight ? depthLeft : depthRight); 
	} 
	return depthval; 
}
```

##### 复制二叉树
![[Pasted image 20230927192656.png]]
![[Pasted image 20230927192727.png]]
生成一个二叉树的结点 (其数据域为item,左指针域为lptr,右指针域为rptr)
```c
BiTNode *GetTreeNode(TElemType item, BiTNode *lptr , BiTNode *rptr ) { 
	if ( !(T = (BiTNode*)malloc(sizeof(BiTNode))) ) 
		exit(1); 
	T-> data = item; 
	T-> lchild = lptr; 
	T-> rchild = rptr; 
	return T; 
}

BiTNode *CopyTree(BiTNode *T) { 
	if (!T ) return NULL; 
	if (T->lchild ) 
		newlptr = CopyTree(T->lchild); // 复制左子树 
	else newlptr = NULL; 
	if (T->rchild ) 
		newrptr = CopyTree(T->rchild); // 复制右子树 
	else newrptr = NULL; 
	newT = GetTreeNode(T->data, newlptr, newrptr); 
	return newT; 
} // CopyTree
```

### 应用：建立二叉树存储结构
![[Pasted image 20230927192910.png]]
```c
Status CreateBiTree(BiTree &T) { 
	scanf(&ch); 
	if (ch==' ') T = NULL; 
	else { 
		if (!(T = (BiTNode *)malloc(sizeof(BiTNode)))) 
			exit(OVERFLOW); 
		T->data = ch; // 生成根结点 
		CreateBiTree(T->lchild); // 构造左子树 
		CreateBiTree(T->rchild); // 构造右子树 
	} 
	return OK; 
} // CreateBiTree
```
![](Pasted%20image%2020231126203644.png)
##### 按数学表达式建树
![[Pasted image 20230927192938.png]]
###### 先缀表达式
![[Pasted image 20230927192951.png]]
由先缀表示式建树的算法的基本操作：
```c
scanf(&ch); 
if ( In(ch, 字母集 )) 
	建叶子结点; 
else { 
	建根结点; 
	递归建左子树; 
	递归建右子树; 
}
```
###### 原表达式
![[Pasted image 20230927193149.png]]
基本操作:
```c
scanf(&ch); 
if (In(ch, 字母集 )) 
	{ 建叶子结点; 暂存; } 
else if (In(ch, 运算符集)) { 
	和前一个运算符比较优先数; 
	若当前的优先数“高”，则暂存; 
	否则建子树; 
}
```
算法（仅供示例，有很多问题）
```c
void CrtExptree(BiTree &T, char exp[] ) { 
	InitStack(S); 
	Push(S, '#'); 
	InitStack(PTR); 
	p = exp; 
	ch = *p; 
	while (!(GetTop(S)=='#' && ch=='#')) { 
		if (!IN(ch, OP)) 
			CrtNode( t, ch ); // 建叶子结点并入栈 
		else { 
			switch (ch) { 
				case '(' : 
					Push(S, ch); 
					break; 
				case ')' : 
					Pop(S, c); 
					while (c!= '(' ) { 
						CrtSubtree( t, c); // 建二叉树并入栈 
						Pop(S, c); 
					} 
					break; 
				default : 
					while(GetTop(S, c) && ( precede(c,ch))) { 
						CrtSubtree( t, c); 
						Pop(S, c); 
					} 
					if ( ch!= '#' ) Push( S, ch); 
					break;
			} // switch		
		} // else
		if ( ch!= '#' ) { p++; ch = *p;} 
	} // while 
	Pop(PTR, T); 
} // CrtExptree

// 建叶子结点的算法：
void CrtNode(BiTree& T,char ch) { 
	T=(BiTNode*)malloc(sizeof(BiTNode)); 
	T->data = ch; 
	T->lchild = T->rchild = NULL; 
	Push( PTR, T ); 
}

// 建子树的算法：
void CrtSubtree (BiTree& T, char c) { 
	T=(BiTNode*)malloc(sizeof(BiTNode)); 
	T->data = c; 
	Pop(PTR, rc); T->rchild = rc; 
	Pop(PTR, lc); T->lchild = lc; 
	Push(PTR, T); 
}
```

##### 由先序和中序序列建树
![[Pasted image 20230927193416.png]]
![[Pasted image 20230927193426.png]]
```c
void CrtBT(BiTree& T, char pre[], char ino[], int ps, int is, int n ) { 
// 已知pre[ps..ps+n-1]为二叉树的先序序列， ino[is..is+n-1]为二叉树的中序序列，本算法由此两个序列构造二叉链表 
	if (n==0) T=NULL; 
	else { 
		k=Search(ino, pre[ps]); // 在中序序列中查询 
		if (k== -1) T=NULL; 
		else { 
			T=(BiTNode*)malloc(sizeof(BiTNode)); 
			T->data = pre[ps]; 
			if (k==is) T->Lchild = NULL; 
			else CrtBT(T->Lchild, pre[], ino[], ps+1, is, k-is ); 
			if (k=is+n-1) T->Rchild = NULL; 
			else CrtBT(T->Rchild, pre[], ino[], ps+1+(k-is), k+1, n-(k-is)-1 );
		} 
	}
} // CrtBT
```

### 层次遍历
```c
Status LevelOrderTraverse(BiTree T) {
	Queue Q;
	InitQueue(Q); 
	if (T) Enqueue(Q,T); 
	while(!QueueEmpty(Q)) {
		DeQueue(Q,&E); 
		visit(E); 
		if (E->lchild) EnQueue(Q,E->lchild); 
		if (E->rchild) EnQueue(Q,E->rchild); 
	} 
}
```

### 线索链表的遍历
![[Pasted image 20230927194244.png]]
	firstNode(T)是头结点，不一定是根结点
	Succ(p)是后继
![[Pasted image 20230927194250.png]]
```c
void InOrderTraverse_Thr(BiThrTree T, void (*Visit)(TElemType e)) { 
	p = T->lchild; // p指向根结点 
	while (p != T) { // 空树或遍历结束时，p==T 
		while (p->LTag==Link) p = p->lchild; // 第一个结点 
		if(!Visit(p->data)) return ERROR;//访问找到的第一个结点 
		while (p->RTag==Thread && p->rchild!=T) { 
			p = p->rchild; 
			Visit(p->data); // 访问后继结点 
		} 
		p = p->rchild; // p进至其右子树根 
	} 
} // InOrderTraverse_Thr
```

# 树和森林的表示方法
---
## 树的存储结构
### 双亲表示法
![[Pasted image 20230927202117.png]]
```c
#define MAX_TREE_SIZE 100
typedef struct PTNode { 
	Elem data; 
	int parent; // 双亲位置域 
} PTNode;
// 结点结构：`data | parent`

// 树结构:
typedef struct { 
	PTNode nodes [MAX_TREE_SIZE]; 
	int r, n; // 根结点的位置和结点个数 
} PTree;
```

### 孩子链表表示法
![[Pasted image 20230927202153.png]]
```c
// 孩子结点结构: `child | next`
typedef struct CTNode { 
	int child; 
	struct CTNode *next; 
} *ChildPtr;

// 带头指针结点结构: `data | firstchild`
typedef struct { 
	Elem data; 
	ChildPtr firstchild; // 孩子链的头指针 
} CTBox;

// 树结构:
typedef struct { 
	CTBox nodes[MAX_TREE_SIZE]; 
	int n, r; // 结点数和根结点的位置 
} CTree;
```

### 二叉链表（孩子-兄弟）表示法
![[Pasted image 20230927203338.png]]
结点结构: `firstchild | data | nextsibling`
```c
typedef struct CSNode{ 
	Elem data; 
	struct CSNode *firstchild, *nextsibling; 
} CSNode, *CSTree;
```

![[Pasted image 20230927203712.png]]
![[Pasted image 20230927204134.png]]
![[Pasted image 20230927204305.png]]
![[Pasted image 20230927204321.png]]

## 树的遍历
树的遍历可有三条搜索路径: 
先根(次序)遍历: 
	若树不空，则先访问根结点，然后依次先根遍历各棵子树。
后根(次序)遍历: 
	若树不空，则先依次后根遍历各棵 子树，然后访问根结点。
按层次遍历: 
	若树不空，则自上而下自左至右 访问树中每个结点。
![[Pasted image 20230927204406.png]]
## 森林的遍历
![[Pasted image 20230927204413.png]]
### 先序遍历
若森林不空，则访问森林中第一棵树的根结点； 
先序遍历森林中第一棵树的子树森林； 
先序遍历森林中(除第一棵树之外)其余树构成的森林。 
即：依次从左至右对森林中的每一棵树进行先根遍历。
![[Pasted image 20230927204512.png]]
### 中序遍历
若森林不空，则中序遍历森林中第一棵树的子树森林； 
访问森林中第一棵树的根结点； 
中序遍历森林中(除第一棵树之外)其余树构成的森林。 
即：依次从左至右对森林中的每一棵树进行后根遍历。
![[Pasted image 20230927204538.png]]

## 树的遍历和二叉树遍历的对应关系
![[Pasted image 20230927204735.png]]

## 树的使用
设树的存储结构为孩子兄弟链表 
```c
typedef struct CSNode{ 
	Elem data; 
	struct CSNode *firstchild, *nextsibling; 
} CSNode, *CSTree;
```
### 求树的深度
```c
int TreeDepth(CSTree T) { 
	if(!T) return 0; 
	else { 
		h1 = TreeDepth( T->firstchild ); 
		h2 = TreeDepth( T->nextsibling); 
		return(max(h1+1, h2));
	} 
} // TreeDepth 
```
### 求路径
![[Pasted image 20230927204900.png]]
```c
void AllPath( Bitree T, Stack& S ) {  
// 输出二叉树上从根到所有叶子结点的路径
	if (T) { 
		Push( S, T->data ); 
		if (!T->Lchild && !T->Rchild ) 
			PrintStack(S); 
		else { 
			AllPath( T->Lchild, S ); 
			AllPath( T->Rchild, S ); 
		} 
		Pop(S);// if也好，else也好，结束后都要pop 
	} // if(T) 
} // AllPath

void OutPath( Bitree T, Stack& S ) { 
// 输出森林中所有从根到叶的路径
	while ( !T ) { 
		Push(S, T->data ); 
		if ( !T->firstchild ) Printstack(s); 
		else OutPath( T->firstchild, s ); 
		Pop(S); 
		T = T->nextsibling; //往右走前要出栈，因为右链域是兄弟 
	} // while 
} // OutPath 
```

### 建树的存储结构
![[Pasted image 20230927204939.png]]
![[Pasted image 20230927204957.png]]
```c
void CreatTree( CSTree &T ) { 
	T = NULL; 
	for( scanf(&fa, &ch); ch!='#'; scanf(&fa, &ch);) { 
		p = GetTreeNode(ch); // 创建结点 
		EnQueue(Q, p); // 指针入队列 
		if (fa == '#') T = p; // 所建为根结点 
		else {  // 非根结点的情况 
			GetHead(Q,s); // 取队列头元素(指针值) 
			while (s->data != fa ) { // 查询双亲结点 
				DeQueue(Q,s); 
				GetHead(Q,s); 
			} 
			if (!(s->firstchild)) 
				{ s->firstchild = p; r = p; } // 链接第一个孩子结点 
			else 
				{ r->nextsibling = p; r = p; } // 链接其它孩子结点
		}
	} // for 
} // CreateTree
```

# 哈夫曼树和哈夫曼编码
---
## 最优树
### 定义
结点的路径长度定义为： 
	从根结点到该结点的路径上分支的数目。
树的路径长度定义为： 
	树中每个结点的路径长度之和。
树的带权路径长度定义为： 
	树中所有叶子结点的带权路径长度之和 
	WPL(T) = $\sum$w<sub>k</sub>l<sub>k</sub> (对所有叶子结点)。
例如：
	在所有含 n 个叶子结点、并带相应权值的 m 叉树中，必存在一棵其带权路径长度取最小值的树，称为“最优树”
![[Pasted image 20230927205344.png]]

## 构造最优树
![[Pasted image 20230927205350.png]]
![[Pasted image 20230927205358.png]]
![[Pasted image 20230927205404.png]]

![[Pasted image 20230927205412.png]]
![[Pasted image 20230927205417.png]]

## 前缀编码
指的是，任何一个字符的编码都不是同一字符集中另一个字符的编码 的前缀。 
利用赫夫曼树可以构造一种不等长的二进制编码，并且构造所得的赫夫曼编码是一种最优前缀编码，即使所传电文的总长度最短。

# 本章小结
---
1. 熟练掌握二叉树的结构特性，了解相应的证明方法。 
2. 熟悉二叉树的各种存储结构的特点及适用范围。 
3. 遍历二叉树是二叉树各种操作的基础。实现二叉树遍历的具体算法与所采用的存储结构有关。掌握各种遍历策略的递归算法，灵活运用遍历算法实现二叉树的其它操作。层次遍历是按另一种搜索策略进行的遍历。
4. 理解二叉树线索化的实质是建立结点与其在相应序列中的前驱或后继之间的直接联系，熟练掌握二叉树的线索化过程以及在中序线索化树上找给定结点的前驱和后继的方法。二叉树的线索化过程是基于对二叉树进行遍历，而线索二叉树上的线索又为相应的遍历提供 了方便。 
5. 熟悉树的各种存储结构及其特点，掌握树和森林与二叉树的转换方法。建立存储结构是进行其它操作的前提，因此读者应掌握 1 至 2 种建立二叉树和树的存储结构的方法。 
6. 学会编写实现树的各种操作的算法。 
7. 了解最优树的特性，掌握建立最优树和哈夫曼编码的方法