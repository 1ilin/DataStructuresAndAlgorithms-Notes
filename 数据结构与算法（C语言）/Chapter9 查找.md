## 重点
顺序查找、二分查找、索引顺序查找、二叉排序树查找以及散列表查找的基本思想和算法实现。
## 难点
二叉排序树的删除算法和平衡二叉树的构造算法。

## 预备知识
---
（1在哪？）
### 2. 类型定义
```c
typedef float Keytype;
//Keytype定义为浮点数据类型 
或者 
typedef int Keytype; 
//Keytype定义为整型数据类型 
或者 
typedef char *Keytype; 
//Keytype定义为字符指针数据类型

数据元素类型定义为： 
typedef struct{
	Keytype key;//关键字
	 … 
}ElemType; // ElemType结构体数据类型
```

### 3. 宏定义
```c
//数值型关键字的比较
#define EQ(a,b) ((a)==(b)) 
#define LT(a,b) ((a)<(b)) 
#define LQ(a,b) ((a)<=(b))
//字符型关键字的比较 
#define EQ(a,b) (!strcmp((a),(b))) 
#define LT(a,b) (strcmp((a),(b))<0) 
#define LQ(a,b) (strcmp((a),(b)<=0)
```

# 静态查找表
---
## 顺序表的查找
### 存储结构
```c
typedef struct {
	ElemType *elem;
	// 数据元素存储空间基址，建表时按实际长度分配
	int length; // 表的长度
} SSTable;

SSTable ST;
ST.elem[i].key
```

### 查找思想
(1)从查找表的第一个元素向后（或从最后一个元素向前），比较当前位置数据元素的关键字与查找关键字；
(2)若相等，输出当前位置，查找成功，若不相等，走向下一个位置； 
(3)循环执行(1)、(2)步，直到查找成功或超出范围，表示查找失败。

### 实例演示
![[Pasted image 20231030174347.png]]
![[Pasted image 20231030174434.png]]

### 算法实现
在顺序表ST中顺序查找其关键字等于key的数据元素。
若找到，则函数值为该元素在表中的位置，否则为0。
```c
int Search_Seq(SSTable ST, KeyType key) {
	ST.elem[0].key = key; // “哨兵”
	for (i=ST.length; ST.elem[i].key!=key; --i); // 从后往前找
		return i; // 找不到时，i为0
} // Search_Seq
```

### 算法分析
![[Pasted image 20231030182847.png]]
![[Pasted image 20231030182911.png]]

## 有序表的查找（折半查找）
---
### 折半查找的存储要求
折半查找的前提要求是顺序存储并且有序。
### 折半查找的思想
(1)、将要查找的关键字与查找表中间的元素的关键字进行比较，若相等，返回当前位置； 
(2)、若查找关键字比当前位置关键字小， 向前递归，否则向后递归。
### 实例演示
![[Pasted image 20231030183312.png]]
### 算法实现
```c
int Search_Bin ( SSTable ST, KeyType key ) 
{
	low = 0; high = ST.length-1; // 置区间初值
	while (low <= high)
	{
		mid = (low + high) / 2;
		if （EQ (key , ST.elem[mid].key) )
		 return mid; // 找到待查元素
		else if ( LT (key , ST.elem[mid].key) )
		 high = mid - 1; // 继续在前半区间进行查找
		else low = mid + 1; // 继续在后半区间进行查找
	} 
	return 0; // 顺序表中不存在待查元素
} // Search_Bin
```
### 算法分析
#### 平均查找长度
![[Pasted image 20231030183655.png]]
折半查找的时间复杂度为 O($\log$<sub>2</sub>n)
优点在于每查找一次可消去一半的结果，即具有剪枝的能力

#### 查找失败时的外部结点数(判定树共n个节点)
=2n<sub>0</sub>+n<sub>1</sub>
=n<sub>0</sub>+n<sub>0</sub>+n<sub>1</sub>
=n<sub>2</sub>+1+n<sub>0</sub>+n<sub>1</sub>
=n+1

### 递归算法
>来自章末习题
```c
int low=0,up=last;
int BINSEARCH(int low,int up,LIST F)
{
	int mid;
	if(low<=up)
	{	
		mid=(low+up)/2;
		if (K==F[mid].key)
			return mid;
		else
			if (K<F[mid].key)
				return BINSEARCH(low,mid-1,F)
			else
				return BINSEARCH(mid-1,up,F);
	}
	return(-1); 
}
```

## 静态树表的查找（自学）

## 索引顺序表的查找
### 索引顺序表的存储要求
![[Pasted image 20231030184316.png]]
◼线性表的要求
查找表分成n块，当i>j时，第i块中的最小元素的关键字大于第j块中的最大元素的关键字。 
◼索引表的要求
(1) 索引表是顺序存储
(2) 索引表里存储了各个块最大值和开始地址

### 查找思想
(1) 首先确定所要查找关键字在哪一块中; 
(2) 在所确定的块中用顺序查找查找关键字。

### 存储结构
```c
typedef struct{
	ElemType elem;
	int length;
}SSTable;
SSTable ST;
ST.elem[i].key

typedef struct{
	keytype key; //本块最大值
	int addr; //本块开始地址
}indextype;
typedef struct
{ indextype index[maxblock];
 int block;
}INtable;
INtable IX;
```

### 算法实现
```c
int SEARCH(SSTable ST, INtable IX, KeyType key) {
	int i=0,s,e; //s和e分别记录在查找表中的开始位置和结束位置
	while ( (key > IX.index[i].key)&&(i < IX.block) )
		i++; //在索引表中查找,确定块号i
	if (i < IX.block){
		s = IX.index[i].addr;//根据块号确定s
		i==IX.block-1? e=ST.length-1: e=IX.index[i+1].addr-1; //确定e
		while ( key!=ST.elem[s].key&&(s<=e) ) //在查找表中从s到e查找
			s=s+1;
		if (s<=e)
			return s;
	}
	return -1
}
```

### 算法分析
问题：如果索引表长度为b，每块平均长度为L,平均查找长度是多少？ 
	答案：$\frac{b+1}{2}+\frac{L+1}{2}$
问题：长度为n的线性表,平均分成多少块平均查找次数最少?
	答案：$\sqrt{2}$

# 动态查找表
---
## 二叉排序树/二叉查找树 BST
### 定义
二叉排序树(Binary Sort Tree)或者是一棵空树；
或者是具有下列性质的二叉树： 
	若根节点有左子树，则左子树上所有结点关键字的值均小于根结点关键字的值；
	若根节点有右子树，则右子树上所有结点关键 字的值均大于根结点的关键字的值；
	根节点的左、右子树也分别为二叉排序树。
![[Pasted image 20231101170530.png]]
	二叉排序树的关键特征是按中序遍历得到的序列升序排列

### 结点查找
#### 实例
![[Pasted image 20231101170648.png]]

#### 查找思想
(1) 当二叉排序树不空时，先将给定值和根结点的关键字比较，若相等，则查找成功；否则：
(2) 若给定值小于根结点的关键字，则在左子树上继续进行查找；
(3) 若给定值大于根结点的关键字，则在右子树上继续进行查找;
(4) 直到找到或查到空结点时为止。

#### 查找算法
```c
typedef struct BiTNode {
	TElemType data;
	struct BiTNode *lchild, *rchild;
} BiTNode, *BiTree;

BiTree SearchBST(BiTree T, keytype k) {
	BiTree p=T;
	while(p!=NULL){
		if (k==p->data.key) return p;
		if (kdata.key) p=p->lchild;
		if (k>p->data.key) p=p->rchild;
	}
	return NULL;
}
```

#### 算法分析
(1) 二叉排序树的平均查找长度最差情况与顺序表相同(关键字有序时),为O(n); 
(2) 最好情况与折半查找相同,是O(log<sub>2</sub>n)数量级的; 
(3) 二叉排序树的平均查找长度仍然是O(log<sub>2</sub>n)。
![[Pasted image 20231101171255.png]]

###  结点插入
#### 思想
(1) 若二叉树为空： 
	则待插入结点s作为根结点；
(2) 当二叉排序树非空时：
	将待插结点关键字与根结点进行比较:
		若相等，已存在该结点，无须插入；
		若小于根结点，插入左子树；
		若大于根结点，插入右子树。
![[Pasted image 20231101172811.png]]
	25<45，插入45左子树
	25>12，插入12右子树
	25<37，插入37左子树
	25>24，插入24右子树—创建24右子树

#### 插入位置的查找
```c
Status SearchBST(BiTree T, keytype key, BiTree f, BiTree &p)
{//在二叉排序树T上找key，f是T的双亲，p用于记录查找路径上的最后一个结点
//初始时候T的双亲为空，故f为空
	if(!T) {p=f; return FALSE;} //树为空时，p=f ，返回假值
	else if EQ(key,T->data.key){p=T; return TRUE;} //找到,p=T,返回真值
	else if LT(key,T->data.key)
		return SearchBST(T->lchild, key, T, p); //去左子树找
	else
		return SearchBST(T->rchild, key, T, p); //去右子树找
}
```
![[Pasted image 20231101173102.png]]
	函数运行后有三种结果：
	(1) 返回真值表示找到，无需插入。
	(2) 返回假值，p!=NULL, 表示是空树。
	(3) 返回假值，p!=NULL, 插入P的左子树或右子树

#### 插入算法
```c
Status InsertBST(BiTree &T, ElemType e){ /*Status类似Bool*/
	BiTree p,s;
	if (!SearchBST(T, e.key, NULL, p)) // 确定插入位置
	{
		s = (BiTree)malloc(sizeof(BiTNode));
		s->data = e; s->lchild = s->rchild = NULL;
		if (!p) T = s; // p为空，则s为新的根结点
		else if (LT(e.key, p->data.key))
			p->lchild=s; // 插入s为p的左孩子
		else
			p->rchild = s; // 插入s为p的右孩子
		return TRUE;
	}
	else
		return FALSE; // T中已有e，不需要插入
} // Insert BST
```
	从空树开始循环调用插入算法即可生成二叉排序树

#### 算法分析
(1)二叉排序树的插入算法的时间复杂性与查找算法的相同; 
(2)最好情况是O(log<sub>2</sub>n); 
	最坏情况是O(n); 
	平均情况是O(log<sub>2</sub>n)。

### 结点删除
#### 删除的定义
在二叉查找树上的删除一个结点，要求删除结点后， 仍保持二叉排序树的结构特点不变。
#### 删除方法
设被删结点为p，其双亲结点为f，则p可能有以下4种情况：
##### p为叶子结点
释放结点p，修改其父结点f的相应指针。
![[Pasted image 20231101191548.png]]

##### p只有左子树
释放结点p，p的左子树 顶替p点的位置。
![[Pasted image 20231101191629.png]]

##### p只有右子树
释放结点p，p的右子树顶替p点的位置。
![[Pasted image 20231101191654.png]]

##### p左右子树都有
a. 把左子树作为右子树中最小结点的左子树。 
	或者把右子树作为左子树中最大结点的右子树。
![[Pasted image 20231101191823.png]]
	缺点：增加了树的高度。

b. 找一个结点顶替它的位置。 
能够顶替它的结点是左子树中最大的或右子树中最小的。
以用左子树的最大结点来顶替为例：
![[Pasted image 20231101191939.png]]

#### 算法
##### 递归
```c
Status DeleteBST(BiTree &T, KeyType key){
	if (!T) return FALSE;//空树，无法删除
	else {
		if (EQ(key, T->data.key)) //找到
			return Delete(T); //删除T
		else if (LT(key, T->data.key)) //去左子树删
			return DeleteBST(T->lchild, key);
		else //去右子树删
			return DeleteBST(T->rchild, key);
		}
} // DeleteBST
```
##### 分情况讨论
```c
Status Delete(BiTree &p)
{ BiTree q, s;
	//p无右子树,则p为叶子或只有左子树
	if (!p->rchild)
		{q = p; p = p->lchild; free(q);}
	//p只有右子树
	else if (!p->lchild)
		{q = p; p = p->rchild; free(q);}
```
![[Pasted image 20231101192502.png]]
```c
	//p的左右子树都不为空的情况
	else 
		{q = p; s = p->lchild;//s指向p的左子树,q是s的父亲
		while (s->rchild) {q = s; s = s->rchild; } //找最大
		p->data = s->data;//把结点s的数据赋给p的数据
		if (q != p) q->rchild = s->lchild;//执行了while
		else q->lchild = s->lchild;//while未被执行
		free(s);
		}
	return TRUE;
} // Delete
```
![[Pasted image 20231101192830.png]]

#### 算法分析
（1）二叉排序树的删除算法的时间复杂性与查找算法的时间复杂性相同; 
（2）最好情况是 O(log<sub>2</sub>n)；
	最坏情况是 O(n)；
	平均情况是O(log<sub>2</sub>n)。
![[Pasted image 20231101193139.png]]

## 平衡二叉树的构建
由于二叉排序树构建时可能出现退化成线性表的情况，故引入一些限制来使其成为树，如平衡二叉树、红黑树等

### 定义
平衡二叉树也称*AVL树*，一棵平衡二叉树或者是空树，或者是具有下列性质的二叉排序树：它的所有节点的左子树和右子树的高度之差的绝对值不超过1。由定义可知：n个结点的AVL树，其高度可保持在O(log<sub>2</sub>n)，故平均查找长度也可保持在O(log<sub>2</sub>n)。
一棵二叉树的左右子树的高度差(左减右)也叫*平衡因子*(BF--Balance Factor)，可见，AVL树任意结点的平衡因子只能为-1,0和1
![[Pasted image 20231103164434.png]]

### 示例
#### RR旋转（左旋）
因为在**右**子树的**右**子树插入造成的不平衡调整办法称作**RR**旋转
![[Pasted image 20231103170726.png]]

#### LL旋转（右旋）
因为在**左**子树的**左**子树插入造成的不平衡，调整办法称作**LL**旋转
![[Pasted image 20231103170811.png]]

#### LR旋转（先左旋再右旋）
因为在**左**子树的**右**子树插入造成的不平衡，调整办法称作**LR**旋转
![[Pasted image 20231103170848.png]]

#### RL旋转（先右旋再左旋）
因为在**右**子树的**左**子树插入造成的不平衡，调整办法称作**RL**旋转
![[Pasted image 20231103170925.png]]

### 思想+算法
在构造过程中，每当插入一个新结点时，首先检查是否因插入而破坏了树的平衡性。 
若是, 则找出其中最小不平衡子树，在保持二叉排序树特性的前提下, 调整最小不平衡子树中各结点之间的连接关系,以达到新的平衡。 
最小不平衡子树：以离插入结点最近，且平衡因子绝对值大于1的结点作为根的子树。 
假设二叉排序树的最小不平衡子树的根结点为A，则有四种形态需要调整。

>貌似只考原理，不考具体算法
#### RR型— 左旋
```c
void L_Rotate(BSTree &p) //形参的改变对实参有影响，故用引用传递
{
	BSTree rc; //定义转轴结点
	rc = p->rchild; //p为原根结点；让轴为根的右孩子
	p->rchild = rc->lchild; //新的右孩子为轴的左孩子
	rc->lchild = p; //新的左孩子为原先的根结点
	p = rc; //新的根为转轴结点
}
```
![[Pasted image 20231103172042.png]]

#### LL型—右旋
```c
void R_Rotate(BSTree &p)
{
	BSTree lc = p->lchild;
	p->lchild = lc->rchild;
	lc->rchild = p;
	p = lc;
}
```
![[Pasted image 20231103172003.png]]

#### LR型—先左旋后右旋
![[Pasted image 20231103172118.png]]

#### RL型—先右旋后左旋
![[Pasted image 20231103172206.png]]

## B树和B+树（自学）

# 哈希表
---
## 定义
在记录的关键字和存储位置之间建立一个映射*H:K->P*，K是关键字集合，P是存储位置集合，按这种思想建立的线性表称为*哈希表（Hash Table）*，H称为*哈希函数*。基于哈希表的查找称为*哈希查找*，在查找的时候，给定一个关键字*key*，就能确定其存储位置*H(key)*。这种查找方法也称作*散列法*或*杂凑法*。哈希表也叫*散列表*或*杂凑表*，哈希函数也叫*散列函数*或*杂凑函数*。哈希查找期望的时间复杂度是*O(1)*,即常量级的。
![[Pasted image 20231103174824.png]]

## 哈希函数的构造方法
### 直接定址法
取关键字或关键字的某个线性函数值为哈希地址。
即：H(key)=a×key+b
其中a、b为常数。又称H(key)为自身函数。
优点：关键字和地址一一对应，关键字不同则地址不同；
缺点：若关键字集合取值跨度很大，浪费存储空间严重。

![[Pasted image 20231103175241.png]]

### 质数除余法
如果表长为n，取小于或等于n的最大质数m作模，关键字通过m取模运算，所得值作为散列地址。
用质数以减少冲突
![[Pasted image 20231103175340.png]]

### 平方取中法
在不知道关键字的全部情况时，可通过求关键字的平方值扩大差别，然后取中间几位作为哈希地址：
![[Pasted image 20231103175524.png]]

### 其他方法（自学）
#### 折叠法
#### 数字分析法
#### 基数转换法

### 哈希函数/散列函数的选取原则
①运算简单； 
②函数值域不能超过表长；
③尽可能使关键字不同时，散列函数值也不同。

## 处理冲突的方法
### 冲突的定义
设k1和k2是两个不同的关键字，若*H(k1)=H(k2)*, 则称为*冲突*
发生冲突的两个关键字k1和k2称为*同义词*。
![[Pasted image 20231103175847.png]]

### 开放定址法
开放定址又称开放地址，是指还没存入数据的空间。
开放定址法：在哈希表为顺序存储结构时,用一定的方法给冲突的关键字分配开放地址的方法。

开放定址法处理冲突的常用方法:
#### 线性探测再散列法
若H(key)=d的单元发生冲突， 则按下述方法进行探查： 
h<sub>i</sub>(k)=(h(k)+i)%n，n是散列表的长度，1≤i≤n-1
#### 二次探测再散列法
若H(key)=d的单元发生冲突，则按下述方法进行探查： 
h<sub>i</sub>(k)=(h(k)+di)%n，n是散列表的长度， di=12 ,-1 2 ,22 ,-2 2 ,…,+k2 ,-k 2 ,(k≤n/2)
#### 伪随机探测再序列
d<sub>i</sub>=伪随机序列
#### 再哈希法/再散列法
设置RH<sub>i</sub>() i=1,2,…,k,多个哈希函数，当一个哈希函数产生冲突时，顺序用下一个。

#### 示例
![[Pasted image 20231103192738.png]]
![[Pasted image 20231103192805.png]]
![[Pasted image 20231103192832.png]]

### 拉链法/链地址法
把关键字的哈希值相同的记录放在同一个单链表中,称为*同义词链表*。有m个散列地址就有m个链表,同时用指针数组 T[0..m-1]存放各个链表的头指针,凡是散列地址为i的记录都以结点方式插入到以T[i]为指针的单链表中。T中各分量的初值应为空指针。
![[Pasted image 20231103192938.png]]
#### 查找过程
(1) 根据关键字K找到关键字为K的结点所在单链表的首地址；
(2) 在所找到的单链表上进行顺序查找
	若找到返回地址值，否则返回空值。

#### 插入过程
(1) 根据关键字K找到关键字为K的结点所应该存在的单链表的首地址；
(2) 在单链表中查找关键字为K的结点，若找到，则无须插入；
(3) 若没找到，利用头插法或尾插法插入单链表中。

#### 删除过程
(1) 根据关键字K找到关键字为K的结点所应该存在的单链表的首地址；
(2) 在单链表中查找关键字为K的结点的前驱 
	若找到则删除关键字为K的结点，否则返回空

### 课前题示例
![](IMG_20231108_142523.jpg)

## 哈希表的查找及其分析
### 存储结构
```c
#define SUCCESS 1 
#define UNSUCCESS 0 
#define DUPLICATE -1 
int hashsize[] = { 997, ... };
typedef struct {
	ElemType *elem;
	int count; // 当前数据元素个数
	int sizeindex; // hashsize[sizeindex]为当前容量
} HashTable;
```
### 查找算法
```c
Status SearchHash (HashTable H, KeyType K, int &p, int &c)
{
	p = Hash(K); // 求得哈希地址
	while ( H.elem[p].key != NULLKEY && !EQ(K, H.elem[p].key)） 
		collision(p, ++c); // 求得下一探查地址 p
	if (EQ(K, H.elem[p].key))
		return SUCCESS // 查找成功
	else
		return UNSUCCESS; // 查找不成功
} // SearchHash
```
### 算法分析
决定哈希表查找的ASL的3个因素：
(1)选用的哈希函数； 
	不同的散列函数构成的散列表平均查找长度是不同的
(2)选用的处理冲突的方法；
	由同一个散列函数、不同的解决冲突方法构造的散列表，其平均查找长度是不相同的。
(3)哈希表饱和的程度即装填因子α ，α=n/m 
	（n—记录数，m—表的长度）。
一般情况下，可以认为选用的哈希函数是“均匀”的，也就是在讨论ASL时，认为各个位置被存数据的概率是相等的在这种情况下，哈希表的ASL是处理冲突方法和装载因子的函数。
![[Pasted image 20231108164020.png]]

### 散列法与其他方法的比较
散列法是根据关键字直 接求出地址的查找方法， 其查找的期望时间为 O(1)。
其他查找方法均是 建立在比较关键字 的基础上。

# 本章小结
---
(1) 熟练掌握顺序查找、二分查找、二叉排序树查找以及散列表查找的基本思想和算法实现。 
(2) 熟练掌握顺序查找、二分查找、二叉排序树查找以及散列表查找的应用条件以及算法优劣。 
(3) 掌握二叉排序树的删除算法和平衡二叉树的构造算法的概要。