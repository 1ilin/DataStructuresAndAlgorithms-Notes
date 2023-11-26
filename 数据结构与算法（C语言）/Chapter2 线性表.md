## 重点
(1)线性表顺序存储； 
(2)线性表的链式存储; 
(3)双向链表； 
(4)循环链表
## 难点
线性表链式存储实现，包括单链表、双向链表、循环链表的基本操作和有关算法。
# 2.1 线性表的定义和特点（逻辑结构）
---
## 定义 
线性表是n个类型相同数据元素的有限序列，通常记作（a<sub>1</sub>,a<sub>2</sub>,a<sub>3</sub>,...,a<sub>n</sub>) 
### 基本特征
(1)集合中必存在唯一的一个“第一元素”； 
(2)集合中必存在唯一的一个 “最后元素”; 
(3)除最后元素之外，均有唯一的后继； 
(4)除第一元素之外，均有唯一的前驱。
每个元素相邻，表中没有空位
![[Pasted image 20230902212332.png]]
## 基本操作
![[Pasted image 20230902212352.png]]
![[Pasted image 20230902212413.png]]
## 基本操作的应用示例
![[Pasted image 20230902213106.png]]
![[Pasted image 20230902213113.png]]
![[Pasted image 20230902213226.png]]
![[Pasted image 20230902213234.png]]
该算法的时间复杂度为O(ListLength(La)+Length(Lb))

# 2.2 顺序表
---
## 定义
用一组地址连续的存储单元依次存放线性表中的数据元素
第一个元素的位置称为线性表的起始地址/基地址
![[Pasted image 20230902213657.png]]

## 数据表的实现
---
### 数据元素的数据类型Elementype实现
```c
struct data{
};
typedef struct data Elemtype;
```
![[Pasted image 20230902214016.png]]
```c
typedef struct data{
}Elemtype;
```
![[Pasted image 20230902214038.png]]
```c
typedef struct{
}Elemtype;
```
![[Pasted image 20230902214050.png]]
### 顺序表数据类型的实现
静态数组实现
![[Pasted image 20230902220025.png]]
### 顺序表的实现
![[Pasted image 20230902220059.png]]
### 静态数组与动态数组的说明
![[Pasted image 20230902221136.png]]
### 顺序表数据类型的动态数组实现
![[Pasted image 20230902221242.png]]

## 基本操作的实现
---
### 构造一个空的线性表
```c
Status InitList_Sq( SqList &L ) {  
	L.elem = (ElemType*) malloc (LIST_INIT_SIZEsizeof (ElemType)); 
	if (!L.elem) exit(OVERFLOW); 
	L.length = 0; 
	L.listsize = LIST_INIT_SIZE; 
	return OK;
} // InitList_Sq
```
应用时： 先声明线性表变量 Sqlist L; 然后调用函数InitList_Sq(L);
时间复杂度：O(1)

### 销毁线性表
```c
Status DestroyList( SqList &L ) {  
	free(L.elem); 
	return ok;
} // InitList_Sq
```
### 判断线性表是否为空
```c
Status ListEmpty( SqList L ) {  
	if(L.length==0) 
		return TRUE; 
	Else 
		return FALSE;
} // InitList_Sq
```
### 求线性表长度
```c
Status ListLength( SqList L ) {  
	return L.length;
} // InitList_Sq
```
### 求前驱的值
```c
Status PriorElem( SqList L, int cur_e, Elemtype &pre_e ) { 
	if (cur_e <= 0||L.length == 0 || cur_e > L.length) 
		return error; 
	else 
		pre_e=L.elem[cur_e-1]; 
}
```
应用时： 
	线性表 L 变量cur_e必须赋值; 
	同时声明变量Elemtype a; 
调用方式： PriorElem( L, cur_e, a) ;

### 插入元素
#### 思想
待插入位置往后的元素位置+1，待插入位置插入元素
#### 算法
```c
// a.数组下标控制
Status ListInsert_Sq(SqList &L, int i, ElemType e) { 
	插入操作// 在顺序表L的第 i 个元素之前插入新的元素e  
	for (j = L.length-1; j >= i-1; --j) 
		L.elem[j+1]=L.elem[j];// 插入位置及之后的元素右移 
		L.elem[i-1]=e // 插入e 
		++L.length; // 表长增1 
	return OK;
} // ListInsert_Sq

// b.指针控制
Status ListInsert_Sq(SqList &L, int i, ElemType e) { 
	插入操作// 在顺序表L的第 i 个元素之前插入新的元素e  
	q = &(L.elem[i-1]); // q 指示插入位置 
	for (p = &(L.elem[L.length-1]); p >= q; --p) 
		*(p+1) = *p; // 插入位置及之后的元素右移 
		*q = e; // 插入e 
		++L.length; // 表长增1 
	return OK;
} // ListInsert_Sq

// 插入操作
{
	if (i < 1 || i > L.length+1) return ERROR; // 插入位置不合法
	if (L.length >= L.listsize) { // 当前存储空间已满，增加分配 
		newbase = (ElemType *)realloc(L.elem, (L.listsize+LISTINCREMENT)*sizeof (ElemType)); 
		if (!newbase) exit(OVERFLOW); // 存储分配失败 
			L.elem = newbase; // 新基址 
			L.listsize += LISTINCREMENT; // 增加存储容量
}
```
#### 指针控制举例
![[Pasted image 20230902222053.png]]
#### 时间复杂度分析
![[Pasted image 20230902222111.png]]

### 删除元素
#### 思想
删除元素的位置赋下一个位置的元素值，后面的元素再不断往前赋值
#### 算法
```c
// 指针控制
Status ListDelete_Sq(SqList &L, int i, ElemType &e) {  
	if ((i < 1) || (i > L.length)) return ERROR; // 删除位置不合法	
	p = &(L.elem[i-1]); // p 为被删除元素的位置 
	e = *p; // 被删除元素的值赋给 e 
	q = L.elem+L.length-1; // 表尾元素的位置 
	for (++p; p <= q; ++p) 
		*(p-1) = *p; // 被删除元素之后的元素左移 
	--L.length; // 表长减1 
	return OK;  
} // ListDelete_Sq

// 数组下标控制
Status ListDelete_Sq(SqList &L, int i, ElemType &e) {
	if ((i < 1) || (i > L.length)) return ERROR; // 删除位置不合法
	e = L.elem[i-1]; //记录删除的元素
	for (j=i-1; j<L.length-1; j++) 
		L.elem[j]=L.elem[j+1]; // 被删除元素之后的元素左移
	--L.length; // 表长减1
	return OK;
} // ListDelete_Sq
```
#### 指针控制举例
![[Pasted image 20230902222403.png]]
#### 时间复杂度分析
![[Pasted image 20230902222345.png]]
算法时间复杂度为: O( ListLength(L))

## 顺序表的优缺点
优点 
	a.节省存储空间; 
	b.对线性表中第i个结点的操作易于实现; 
	c.容易查找一个结点的前驱和后继。
缺点 
	a.插入和删除操作需要移动数据; 
	b.建立空表时，较难确定所需的存储空间。

# 2.3 链表
---
## 单链表
### 定义
一个线性表由若干个结点组成，每个结点至少含有两个域：数据域(信息域)和指针域(链域)，由这样的结点存储的线性表称作链表。
### 结构特点
逻辑次序和物理次序不一定相同； 
元素之间的逻辑关系用指针表示；
需要额外空间存储元素之间的关系。
![](Pasted%20image%2020231125235430.png)
### 头指针
![[Pasted image 20230906190138.png]]

### 头结点
![[Pasted image 20230906190154.png]]

### 单链表的c语言实现
![[Pasted image 20230906190218.png]]
#### 构造空链表
```c
// 构造一个空的线性表L
InitList( &L )
LinkList L； 

// a.带头节点
void InitList(LinkList &L){ 
	L=(Lnode *) malloc (sizeof (Lnode)); 
	L->next=NULL; 
}

// b.不带头节点
void InitList(LinkList &L){ 
	L=NULL;
}

//判断线性表L是否空
ListEmpty(L)

// a.带头节点
int ListEmpty( LinkList L ){ 
	if (L->next==NULL) 
		return TRUE; 
	else 
		return FALSE; 
}

// b.不带头节点
int ListEmpty( LinkList L ){ 
	if (L==NULL) 
		return TRUE; 
	else 
		return FALSE; 
}
```

### 基本操作
![[Pasted image 20230906190518.png]]
![[Pasted image 20230906190524.png]]

#### 取值/查找元素
![[Pasted image 20230906190557.png]]
```c
Status GetElem_L(LinkList L, int i, ElemType &e) {  
	p = L->next; j = 1; // p指向第一个结点，j为计数器 
	while (p && j<i) //顺指针向后查找,直到 p 指向第 i 个元素或为p空
	{ p = p->next; ++j; } 
	if (!p||j>i) // i≥1,j的初始值是1，j>i表示i<1是不合理的 
		return ERROR; // 第 i 个元素不存在 
	e = p->data; // 取得第 i 个元素 
	return OK;
}
```
时间复杂度为: O(ListLength(L))

#### 插入元素
![[Pasted image 20230906190355.png]]

![[Pasted image 20230906190700.png]]
```c
Status ListInsert_L(LinkList L, int i, ElemType e) { 
// L 为带头结点的单链表的头指针，本算法在链表中第i 个结点之前插入新的元素 e  
	p = L; 
	j = 0; 
	while (p && j < i-1) 
		{ p = p->next; ++j; } // 寻找第 i-1 个结点 
	if (!p ||j>i-1) 
		return ERROR; // i 大于表长或者小于1 
	s = (LinkList) malloc ( sizeof (LNode)); 
	s->data = e; 
	s->next = p->next; 
	p->next = s; // 插入
} // LinstInsert_L
```
时间复杂度为：O(ListLength(L))

#### 删除元素
![[Pasted image 20230906190418.png]]

![[Pasted image 20230906190729.png]]
```c
Status ListDelete_L(LinkList L, int i, ElemType &e) {  
	p = L; 
	j = 0; 
	while (p->next && j < i-1) 
		{ p = p->next; ++j; } // p的后继代表要删除的元素，因此p->next不能为空 
	if (!(p->next) ||j>i-1) // i不在1和表长之间 
		return ERROR; // 删除位置不合理 
	q = p->next; 
	p->next = q->next; 
	e = q->data; 
	free(q); 
	return OK;
} // ListDelete_L							 
```
时间复杂度为: O(ListLength(L))
#### 清空链表
```c
void ClearList(&L) { // 将单链表重新置为一个空表 
	while (L->next) { 
		p=L->next; 
		L->next=p->next; 
		free(p);
	} 
} // ClearList  
```
时间复杂度：O( ListLength(L) )

### 建立单链表的方式
#### 头插法
![[Pasted image 20230906190958.png]]
	元素从后往前生成，结点从前往后

#### 尾插法
![[Pasted image 20230906191017.png]]
	r永远指向当前最后的结点
	元素、结点生成顺序一致

## 其他类型的链表
---
### 静态链表

### 循环链表
#### 概念
单链表最后一个结点的指针域没有利用，如果使其指向头指针（头结点），则首尾构成一个循环，称作循环链表。
#### 结构
![](Pasted%20image%2020231126002644.png)
#### 特点
![[Pasted image 20230906191147.png]]
	有尾指针时查找最后一个结点更容易（不需要遍历链表）

#### 单链表和循环链表操作比较
![[Pasted image 20230906192031.png]]
	La、Lb都是带头结点的单链表，Lb接在La之后，时间复杂度是 O(length(La))

![[Pasted image 20230906192041.png]]
La、Lb都是带头结点头指针的单循环链表，实现将Lb接在La之后还形成一个循环链表，时间复杂度是 O(length(La)+length(Lb))。

![[Pasted image 20230906192050.png]]
	La、Lb都是带头结点尾指针的单循环链表，实现将Lb 接在La之后还形成一个循环链表，时间复杂度是 O(1)

### 双向链表
#### 概念
一个链表的每一个结点含有两个指针域：一个指针指向其前驱结点，另一个指针指向其后继结点，这样的链表称为双向链表。
#### 结构
![](Pasted%20image%2020231126141540.png)

#### 特点
优点
	可以双向查找线性表。 
缺点
	进一步增加了内存负担，操作难度加大了。

#### 地址关系
`p->prior`指向p的前驱，`p->next`指向p的后继 
以下都是p结点的地址： `p->next->prior` 和 `p->prior->next`

#### 双向链表的C语言实现
```c
//类型定义 
typedef struct DuLNode{ 
	ElemType data; 
	struct DuLNode *prior; 
	struct DuLNode *next; 
} DuLNode,*DuLinkList;
```
#### 基本操作
##### 查询
和单链表相同
##### 插入节点
![[Pasted image 20230906192356.png]]
	先动待插元素的指针
```c
Status ListInsert_Dul(DuLinkList &L, int i ,ElemType e) { 
	if(!(p=GetElemP_DuL(L,i))) 
		return error; 
	if(!(s=(DuLinkList)malloc(sizeof(DuLNode)))) 
		return error; 
	s->data=e; 
	s->prior=p->prior; 
	s->next=p; 
	p->prior->next=s; 
	p->prior=s; 
	return OK; 
}
```
##### 删除节点
![[Pasted image 20230906192438.png]]
```c
Status ListDelete_Dul(DuLinkList &L,int i ,ElemType &e) { 
	if(!(p=GetElemP_DuL(L,i))) 
		return error; 
	e=p->data; 
	p->prior->next=p->next; 
	p->next->prior=p->prior; 
	free(p); 
	return OK; 
}
```
## 链表的优缺点
---
### 优点
① 插入和删除不需要移动数据
	修改表的步骤时间复杂性低，但查找元素的步骤时间复杂性高
② 不需预先分配空间
	有多少元素申请多少空间
### 缺点
① 指针占用存储空间，增加了内存负担
② 只能顺序查找（操作）

# 线性表的应用——一元多项式的表示
---
一元多项式既可以用顺序表存储，也可以用链表存储，在用顺序表存储时，如果是稀疏多项式则浪费空间严重，用链式存储比较好。
![](Pasted%20image%2020231126144014.png)
```c
// 抽象数据类型
ADT Polynomial { 
数据对象： 
	D＝{ ai | ai ∈TermSet, i=1,2,...,m, m≥0 
		TermSet 中的每个元素包含一个 表示系数的实数和表示指数的整数 }
数据关系： 
	R1＝{ |ai-1 ,ai∈D, i=2,...,n 
		且ai-1中的指数值＜ai中的指数值 } 
基本操作：
	CreatPolyn ( &P, m ) //创建多项式 
	DestroyPolyn ( &P ) //销毁多项式 
	PrintPolyn ( &P ) //打印输出多项式 
	PolynLength( P ) //求多项式的项数 
	AddPolyn ( &Pa, &Pb ) //两个多项式相加 
	SubtractPolyn ( &Pa, &Pb )//两个多项式相减 
	……
} ADT Polynomial

// C语言实现
typedef struct { // 项的表示 
	float coef; // 系数 
	int expn; // 指数 
} term, ElemType; //term用于本ADT，ElemType为LinkList的数据元素 
typedef LinkList polynomial; // 用带表头结点的有序链表表示多项式
```
# 本章小结
---
熟练掌握： 
(1)线性表的逻辑结构特性和线性表的(ADT)设计； 
(2)线性表的顺序存储结构和链式存储结构两种实现方 式和基本操作； 
(3)双向链表的插入和删除等基本操作及相关算法; 
(4)循环链表的特点及相关操作; 
(5)理解一元多项式的表示方法及相加算法，提高自己 用线性表解决实际问题的应用水平。