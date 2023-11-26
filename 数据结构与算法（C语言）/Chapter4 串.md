# 4.1 串类型的定义

# 4.2 串的表示和实现
---
## 1.定长顺序表示
```c
#define MAXSTRLEN 255 // 用户可在255以内定义最大串长 
typedef unsigned char Sstring[MAXSTRLEN+1]; // 0号单元存放串的长度 
Sstring S;
```
## 2.堆分配表示
```c
typedef struct { 
	char *ch; // 若是非空串，则按串长分配存储区，否则ch为NULL 
	int length; // 串长度 
} HString;
```

## 3.块链表示
### 存储密度
![[Pasted image 20230916204358.png]]
结论：采用普通链表存储字符串，存储密度非常低，浪费空间严重。
解决办法：一个结点存储多个字符。这就是串的块链存储。

### 块链存储串
```c
#define CHUNKSIZE 80 
typedef struct Chunk { // 结点结构 
	char ch[CUNKSIZE]; 
	struct Chunk *next; 
} Chunk; 
typedef struct { // 串的链表结构 
	Chunk *head, *tail; // 串的头和尾指针 
	int curlen; // 串的当前长度 
} LString;
```

# 4.3 串的模式匹配算法
## 简单算法（Brute-Force)
串匹配(查找)的定义
`INDEX (S, T, pos)`
初始条件：
	串S和T存在，T是非空串， 1≤pos≤StrLength(S)。
操作结果：
	若主串S中存在和串T值相同的子串返回它在主串S中第pos个字符之后第一次出现的位置；否则函数值为0
```c
int Index(SString S, SString T, int pos) { 
	i = pos; 
	j = 1; 
	while (i <= S[0] && j <= T[0]) { 
		if (S[i] == T[j]) { ++i; ++j; } // 继续比较后继字符 
		else { i = i-j+2; j = 1; } // 指针后退重新开始匹配 
	} 
	if (j > T[0]) return i-T[0]; 
	else return 0; 
} // Index
```
### 时间复杂性分析
![](Pasted%20image%2020231126170738.png)
若第i趟比较成功，共比较了多少次？ 
	假设从第i个位置匹配成功，前i-1次共比较了i-1次。 
	第i次比较了m次,共比较了i+m-1次。 
	i从1到n-m+1,共(n+m)(n-m+1)/2 
	平均需比较(n+m)/2 
	最好的情况平均时间复杂度为O(n+m)

![](Pasted%20image%2020231126170713.png)
若第i趟比较成功，共比较了多少次？ 
	前i-1趟比较每次都比较m次，第i趟也比较m次，共im次， 
	i从1到n-m+1，共比较了(n-m+2)(n-m+1)m/2 
	平均比较次数(n-m+2)m/2 
	最坏的情况时间复杂度为O(n×m)

## KMP算法
![[Pasted image 20230916204943.png]]
![[Pasted image 20230916204948.png]]
![[Pasted image 20230916204956.png]]
![[Pasted image 20230916205004.png]]
![[Pasted image 20230916205009.png]]

![[Pasted image 20230916210338.png]]
![[Pasted image 20230916205038.png]]
![[Pasted image 20230916205045.png]]
### KMP算法
```c
int Index_KMP (SString S,SString T, int pos) { 
	i= pos,j =1; 
	while (i<=S[0] && j<=T[0]) 
		if (j==0 || S[i]==T[j]) { i++;j++; } 
			else j=next[j]; //i不变,j后退 
		if (j>t[0]) return i-t[0]; //匹配成功 
			else return 0; //返回不匹配标志 
}
```
### 求next函数
![[Pasted image 20230916210400.png]]
![[Pasted image 20230916210406.png]]

```c
void get_next(SString T,int next[]) { 
	int j=1,k=0; // 初值表示一开始就匹配不上，是迭代计算next值的基础。
	next[1]=0; 
	while(j<T[0]) { 
		if(k==0||T[j]==T[k]) 
			{++j; ++k; next[j]=k;}/*next[++j]=++k*/ 
			else k=next[k] ;/*T[j]≠T[k]时，k跳转到next[k]*/ 
	} 
}
```
`代码中if的含义就是在 next[j]=k的情况下,如果 T[j]==T[k],则next[j+1]=k+1 或者k=0时，让next[j+1]=1， 表示j>1时候不存在相同的最 长前后缀串即++k后k=1即 next公式的其他情况`
### 求next函数的改进算法
![[Pasted image 20230916210442.png]]

```c
void get_nextval(SString &T,int nextval[]) { 
	int i=1,j=0,nextval[1]=0; 
	while(i<T[0]) { 
		if(j==0||T[i]==T[j]) {
			++i; ++j; 
			if(T[i]!=T[j]) nextbal[i] = j;
			else nextval[i] = nextval[j] ;
		} 
		else j = nextval[j];
	}
}
```

# 本章小结
---
熟练掌握： 
(1)串的定义、性质和特点； 
(2)ADT串的设计、实现方法和基本操作； 
(3)朴素模式匹配算法