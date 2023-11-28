## 重点
(1)图的定义、术语和性质； 
(2)图的邻接矩阵、邻接表的存储结构及其构造方法； 
(3)图的两种遍历方法：深度优先遍历和广度优先遍历； 
(4)最小生成树的算法、拓扑排序的算法； 
(5)关键路径的算法，构造最短路径的Dijkstra算法和 Floyed算法。

## 难点
(1) 有向无环图的关键路径算法;
(2) 求最短路径的Dijkstra算法和Floyed算法。

# 7.1 图的定义和术语（自学）
---
## 图的定义
图G由两个集合构成，记作G= 其中V是顶 点的非空有限集合，E是边的有限集合，其中边是顶 点的无序对或有序对集合。
![[Pasted image 20231030191827.png]]
尖括号<>表示有序对
圆括号()表示无序对

## 图的有关术语
### 无向图(Undigraph) 
在图G中，若所有边是无向边，则称G为无向图； 
### 有向图(Digraph) 
在图G中，若所有边是有向边，则称G为有向图；
	有向边也称为弧(Arc)。
![[Pasted image 20231030192006.png]]

### 无向完全图(Undirected Complete Graph)
无向图且边数为n(n-1)/2，则称其为无向完全图；
### 有向完全图(Directed Complete Graph)
有向图且边数为n(n-1) ，则称其为有向完全图
![[Pasted image 20231030192148.png]]

### 邻接点与关联边
邻接点：边的两个顶点，边上的两个顶点互为邻接点
关联边：若边e= (v, u), 则称顶点v、u 关连边e
![[Pasted image 20231030192421.png]]

### 顶点的度
一个顶点v的度是与它相关联的边的条数，记作D(v)。 
顶点v的入度: 是以v为终点的有向边的条数, 记作ID(v); 
顶点v的出度: 是以v为始点的有向边的条数,记作 OD(v)。

### 顶点数、边数e和度数D（v）之间的关系
![[Pasted image 20231030192651.png]]

### 无向图的路径、回路
无向图G=（V，E）中的顶点序列v1 ,v2 ,… ,vk ,
若 (vi ,vi+1) $\in$ E( i=1,2,…k-1), v =v1 , u =vk , 则称该序列是从顶点v到顶点u的路径； //E前面是‘属于’符号，下面同理
若v=u，则称该序列为回路。
![[Pasted image 20231030192840.png]]

### 有向图的路径、回路
有向图D=（V，E）中的顶点序列v1 ,v2 ,… ,vk , 
若<vi,vi+1> $\in$ E (i=1,2,…k-1), v =v1 , u =vk , 则称该序列是从顶点v到顶点u的路径；
若v=u，则称该序列为回路。
![[Pasted image 20231030193001.png]]
简单路径和简单回路: 
在一条路径中,若除起点和终点外,所有顶点各不相同,则称该路径为简单路径；
由简单路径组成的回路称为简单回路。

### 连通图
在**无向图** G=< V, E >中，若对任何两个顶点 v、u 都存在从v 到 u 的路径，则称G是连通图。
![[Pasted image 20231030193118.png]]

### 强连通图
在**有向图** G=< V, E >中，若对任何两个顶点 v、u 都存在从v到u的路径，则称G是强连通图。
![[Pasted image 20231030193212.png]]

### 子图
设有两个图G=（V，E）、G1 =（V1，E1），
若V1 $\subseteq$ V，E1 $\subseteq$ E，E1关联的顶点都在V1中，则称 G1是G的子图；
![[Pasted image 20231030193326.png]]

### 连通分量
**无向图**中的极大联通子图。
![[Pasted image 20231030193534.png]]
### 强连通分量
**有向图**中的极大强连通子图。
![[Pasted image 20231030193550.png]]

### 网络（边带权图）
某些图的边具有与它相关的数, 称之为权。 这种带权图叫做网络。
![[Pasted image 20231030193618.png]]

### 生成树
连通图的一个子图如果是一棵包含G的所有顶点 的树，则该子图称为G 的生成树。
T是G 的生成树当且仅当T满足如下条件：
	T是G 的连通子图
	T包含G 的所有顶点
	T中无回路

## 图的抽象数据类型定义
```c
ADT Graph {
	数据对象V：V是具有相同特征的数据元素的集合,称为顶点集。
	数据关系R：R={VR} 
			  VR={<v,w>︱v,w∈V且P(v,w)
} ADT Graph
```
### 基本操作
```c
CreatGraph(&G) //建立图
DestroyGraph(&G) //销毁图
InsertVex(&G, v) //插入顶点
DeleteVex(&G, v) //删除顶点
DFSTraverse(G, v, Visit()) //深度优先搜索
BFSTraverse(G, v, Visit()) //广度优先搜索
FirstAdjVex(G, v) //返回 v 的“第一个邻接点”
NextAdjVex(G, v, w) //返回 v 的（相对于 w 的）下一个邻接点
LocateVex(G, u); //返回 u在G中的位置
GetVex(G, v); //返回 v 的值。
PutVex(&G, v, value); //对 v 赋值value
InsertArc(&G, v, w) //增加边,无向图还要增加<w,v>
DeleteArc(&G, v, w) //删除边,无向图还要删除<w,v>
```

# 7.2 图的存储结构
---
图的存储结构至少要保存两类信息： 
	(1)顶点的数据信息 
	(2)顶点间的关系信息
约定: 
	图G=<V, E>, V={v0 ,v1 ,v2 , … vn-1 },顶点的角标为它的编号
## 数组表示（邻接矩阵）
### 邻接矩阵
![[Pasted image 20231030195909.png]]
### 无向图的邻接矩阵
![[Pasted image 20231030195959.png]]
### 有向图的邻接矩阵
![[Pasted image 20231030200026.png]]
### 网络的邻接矩阵
![[Pasted image 20231030200058.png]]

### 图的类型实现
```c
#define INFINITY INT_MAX //最大值，表示无穷大
#define MAX_VERTEX_NUM 100 //最大顶点个数
Typedef enum {DG, DN, UDG, UDN} GraphKind;
	//  枚举{有向图,有向网,无向图,无向网}
typedef struct ArcCell {// 弧的定义
VRType adj; // 表示顶点间的关系,如有边取1，无边取0，
InfoType *info; // 该弧相关信息的指针
} ArcCell, AdjMatrix[MAX_VERTEX_NUM][MAX_VERTEX_NUM];

typedef struct {// 图的类型定义
	VertexType vexs[MAX_VERTEX_NUM]; // 顶点信息
	AdjMatrix arcs;//弧信息
	//VRType arcs[MAX_VERTEX_NUM ][MAX_VERTEX_NUM ];  课件30、31页，两个图的弧定义不一样，不懂
	int vexnum, arcnum; // 顶点数，弧数
	GraphKind kind; // 图的种类标志
} MGraph; 
MGraph G;
```
### 建立无向网络(边带权图)G
#### 思想
首先读入顶点数`G.vexnum`，边数`G.arcnum`，图的种类`G.kind` 
1、给顶点域赋值：`G.vexs[i]`; 
2、给边域赋值： 
① 边域初始化：`G.arcs[i][j].adj=INFINITY`;
② 读入边及权重i, j, w, 
`G.arcs[i][j].adj=w; G.arcs[j][i].adj=w`。
#### 代码
```c
void Creatgraph(MGraph &G){
	int i, j, k; float w;
	scanf(“%d,%d,%d”,G.vexnum, G.arcnum, G.kind);
	for(i=1;i<=G.vexnum;i++)//建立各个顶点,设顶点为char类型
		G.vexs[i]=getchar();
	for(i=1;i<=G.vexnum;i++)//初始化边矩阵
		for(j=1;j<=G.vexnum;j++) {
			G.arcs[i][j].adj=INFINITY;
			G.arcs[i][j].info=NULL; }
	for (k=1;k<=G.arcnum;k++){//录入各条边
		scanf(“%d,%d,%f”,&i,&j,&w);
		G.arcs[i][j].adj=w; G.arcs[j][i].adj=w;}//注意对称赋值
}
```
#### 算法分析
空间开销O(n<sup>2</sup>)
顶点的插入删除操作不容易
边/弧的删除和插入操作容易

## 邻接表
### 定义
![[Pasted image 20231030202720.png]]
#### 无向图的邻接表
![[Pasted image 20231030202806.png]]
#### 有向图的邻接表
![[Pasted image 20231030202829.png]]
#### 有向图的逆邻接表
![[Pasted image 20231030202859.png]]

### 邻接表的存储实现
```c
#define MAX_VERTEX_NUM 20
// (1)顶点表的存储类型： 
typedef struct vnode {
	vertextype data; // 顶点信息
	Arcnode *firstarc; // 指向第一条依附该顶点的弧
} vnode, adjlist[MAX_VERTEX_NUM];
// (2)边表的存储类型：
typedef struct ArcNode {
	int adjvex; // 该弧所指向的顶点的位置
	VRType adj; // 弧<v1,v2>的权值，描述带权图时候需要
	struct ArcNode *nextarc; // 指向下一条弧的指针
	InfoType *info; // 该弧相关信息的指针
} ArcNode;
// (3)图的邻接表存储类型： 
typedef struct {
	adjlist vertices;
	int vexnum, arcnum;
	int kind; // 图的种类标志
} ALGraph;
	ALGraph G;
```
建立邻接表时，节点顺序由采用的是头插法还是尾插法决定

### 建立无向网络(边带权图)G的邻接表
#### 思想
(1) 给顶点表中顶点域赋值，指针域赋值
G.vertices[i].data=getchar();//
G.vertices[i].firstarc=NULL;
(2) 读入边的邻接顶点编号i,j及权w： 
① 生成结点s;//建立边并赋权
前插法建表；s->nextarc= G.vertices[i].firstarc; 
					 G.vertices[i].firstarc=s; 
②生成结点s; //建立边并赋权
前插法建表；s->nextarc= G.vertices[j].firstarc; 
					 G.vertices[j].firstarc=s; 
(3)重复第二步
![[Pasted image 20231030203615.png]]
#### 代码
```c
void Creatadjlist(ALGraph &G){
	int i,j,k;ArcNode *s;
	scanf(“%d,%d,%d”,G.vexnum,G.arcnum,G.kind);
	for(i=1;i<=G.vexnum;i++){
		G.vertices[i].data=getchar();
		G.vertices[i].firstarc=NULL;
	}
	for(k=0;k<G.arcnum;k++){
		scanf(“%d%d%d”,&i,&j,&w);
		s=(ArcNode *)malloc(sizeof(ArcNode));
		s->adjvex=j;
		s->adj=w;
		s->info=NULL;
		s->nextarc= G.vertices[i].firstarc;
		G.vertices[i].firstarc=s;
		…………//建立<j,i>
		}
}
```
#### 算法分析
(1)对于顶点多边少的图采用邻接表能存储节省空间；
	空间复杂度O(n+e)
(2)容易找到任一顶点的第一个邻接点。

## 十字链表*
### 图例
![[Pasted image 20231030205644.png]]
![[Pasted image 20231030205702.png]]
![[Pasted image 20231030205718.png]]
![[Pasted image 20231030205736.png]]

### 存储实现
```c
#define MAX_VERTEX_NUM 20
// (1)边表的存储类型：
typedef struct ArcBox {
	int tailvex, headvex; // 该弧的尾和头位置
	struct ArcBox *hlink, *tlink;
		// 分别为弧头相同和弧尾相同的弧的链域
	InfoType *info; // 该弧相关信息的指针
} ArcBox;
// (2)顶点表的存储类型：
typedef struct Vexnode {
	VertexType data; // 顶点信息
	ArcBox *firstin, *firstout;
	// 分别指向下一个入边和出边
} Vexnode;
// (3)图的十字链表存储类型： 
typedef struct {
	VexNode xlist[MAX_VERTEX_NUM];
	int vexnum, arcnum; // 节点个数，边个数
} OLGraph;
  OLGraph G;
```

## 邻接多重表*

## 总结
在不同的存储结构下，实现各种操作的效率可能是不同的。所以在求解实际问题时，要根据求解问题所需操作，选择合适的存储结构。
当一个图的边数比较少时，选择哪种存储结构？ ——邻接表

# #课件7-2 7.3 图的遍历
---
图的遍历是指：从图的某一顶点出发访问图中的所有顶点，且每个顶点仅访问一次。
## 深度优先搜索
### 基本思想 - 分治算法
深度优先搜索(Depth First Search)遍历类似于树的先序遍历。 
(1) 首先访问图中某一个顶点Vi，以该顶点为出发点； 
(2) 任选一个与顶点Vi邻接的未被访问的顶点Vj；访问Vj ;
(3) 以Vj为新的出发点继续进行深度优先搜索，直至图中所有和Vi有路径的顶点均被访问到。
	（同时也检查了所有的边）
![[Pasted image 20231108195152.png]]

### 算法
#### 概要
输入：图G，初始访问节点v;
输出：图G的深度优先搜索序列；
(1) 访问v; 改变v的访问标志；
(2) 任选一个与v相邻又没被访问的顶点w;
(3) 从w开始继续进行深度优先搜索。
#### 详解
##### 一般情况
```c
void DFS(Graph G, int v) // 从顶点v出发，深度优先搜索遍历连通图G
{
	visited[v] = TRUE;
	VisitFunc(v); //访问v
	for( w=FirstAdjVex(G, v); w!=0; w=NextAdjvex(G,v,w) )
		if (!visited[w]) // 对v的尚未访问的邻接顶点w
			DFS(G, w); // 递归调用DFS
} // DFS
```
##### 基于G以邻接矩阵为存储结构
```c
void DFS(Graph G, int v) // 从顶点v出发，深度优先搜索遍历连通图 G
{
	visited[v] = TRUE;
	printf(“%c”, G.vexs[v]); //访问v
	for(w=0;w<G.vexnum;w++)
		if (G.arcs[v][w].adj!=0 && !visited[w]) // 对v的尚未访问的邻接顶点w 
			DFS(G, w); // 递归调用DFS
} //DFS
```
##### 对于非连通图
![[Pasted image 20231108200515.png]]
首先将图中每个顶点的访问标志设为 FALSE, 之后搜索图中每个顶点，如果未被访问，则以该顶点为起始点，进行深度优先搜索遍历，否则继续检查下一顶点。
```c
void DFSTraverse(Graph G, Status(*Visit)(int v) )
{
	VisitFunc = Visit;
	int visited[vexnum];
	for (v=0; v<G.vexnum; ++v)
		visited[v] = FALSE; // 访问标志数组初始化
	for (v=0; v<G.vexnum; ++v)
		if (!visited[v]) DFS(G, v); // 对尚未访问的顶点调用DFS
}
```
### 算法分析
假设图中有 n 个顶点，e 条边。
如果用邻接矩阵表示图，则查找每一个顶点的所有的边，所需时间为O(n)，则遍历图中所有的顶点所需的时间为O(n<sup>2</sup>)。
如果用邻接表表示图，沿每个链可以找到某个顶点 v 的所有邻接顶点 w。由于总共有 2e 个边结点，所以扫描边的时间为O(e)。而且对所有顶点递归访问1次，所以遍历图的时间复杂性为O(n+e)。

### 深度优先搜索的功能
(1) 求深度优先生成树
(2) 判断图是否连通
(3) 求图的连通分量

## 广度优先搜索
### 基本思想
广度优先搜索(Breadth First Search)遍历类似于树的按层次遍历。 
(1)首先访问图中某一个指定的出发点vi；
(2)然后依次访问与vi邻接且尚未访问的所有点vi1 ,vi2…vit；
(3)再依次以vi1 ,vi2…vit为顶点，访问它们的未被访问的邻接点，依此类推，直到图中所有顶点均被访问为止。
![[Pasted image 20231108201335.png]]

### 算法
#### 概要
输入：图G，初始访问节点v;
输出：图G的广度优先搜索序列；
(1)设置辅助队列Q, 访问节点v,修改v的标志为T,v入队；
(2) while(!Isempty(Q) ) {
		队头元素出队并置为u；
		访问与u邻接且尚未访问的所有节点；
		修改这些节点的访问标志为T；
		将这些节点入队；
	}
#### 详解
##### 一般情况
```c
void BFSTraVErse(Graph G, Status (*Visit)(int v) )
{
	for ( v=0; v<G.vexnum; ++v)
		visited[v] = FALSE; //初始化访问标志
	InitQueue(Q); // 置空的辅助队列Q
	for ( v=0; v<G.vexnum; ++v )
		if ( !visited[v])  // v 尚未访问
		{
			visited[v] = TRUE; Visit(v); // 访问v
			EnQueue(Q, v); // v入队列
			while (!QueueEmpty(Q))
			{
				DeQueue(Q, u); // 队头元素出队并置为u
				for( w=FirstAdjvex(G, u); w!=0; w=NextAdjvex(G,u,w) )
					if (!visited[w]){
						visited[w]=TRUE;
						Visit(w);
						EnQueue(Q, w);
					}//if
			}//while
		}//if
} // BFSTraVErse

```
##### 基于G以邻接表为存储结构
```c
void BFSTraVErse(Graph G, Status (*Visit)(int v) )
{
	for (v=0; v<G.vexnum; ++v)
		visited[v] = FALSE; //初始化访问标志
	InitQueue(Q); // 置空的辅助队列Q
	for ( v=0; v<G.vexnum; ++v )
		if ( !visited[v]) // v 尚未访问
		{
			visited[v] = TRUE; Visit(v); // 访问v
			EnQueue(Q, v); // v入队列
			while (!QueueEmpty(Q))
			{
				DeQueue(Q, u); // 队头元素出队并置为u
				for(p=G.vertices[u].firstarc;p!=NULL;p=p->nextarc)
					if ( ! visited[p->adjvex])
					{
						visited[w]=TRUE; Visit(w); EnQueue(Q, w);
					}
			}//while
		}//if
} // BFSTraVErse
```
### 算法分析
如果使用邻接表表示图，则循环的总时间代价为 d0 + d1 + … + dn-1 = O(n+e)，其中的 di 是顶点 i 的度。
如果使用邻接矩阵，则对于每一个被访问过的顶点，循环要检测矩阵中的 n 个元素，总的时间代价为O(n<sup>2</sup>)

### 广度优先搜索的功能
(1) 求广度优先生成树
(2) 判断图是否连通
(3) 求图的连通分量。


# 7.4 图的连通性问题
---
## 无向图的连通分量
### 连通分量求解过程
深度优先搜索和广度优先搜索在基于一点的搜索过程中，能访问到该顶点所在的最大连通子图的所有顶点，即一个连通分量。
通过变换搜索顶点，可求得无向图的所有连通分量。

### 深度优先搜索实现找图的连通分量
```c
void DFSTraVErse(Graph G, Status (*Visit)(int v))
{
	int i=1; //连通分量计数器
	VisitFunc = Visit; //访问函数设为Visit
	for (v=0; v<G.VExnum; ++v)
		visited[v] = FALSE; // 访问标志数组初始化
	for (v=0; v<G.VExnum; ++v)
		if (!visited[v]) {
			printf(“第%d个连通分量”,i++);
			DFS(G, v); //从v开始深度优先搜索
		}
}
```
## 有向图的强连通分量*

## 最小生成树
### 定义
对于边带权图(网)来说：在所有的生成树中，各边的权值(边长)总和最小的生成树称作最小生成树。
#### 示例
![[Pasted image 20231108210140.png]]

### 算法
#### Prim算法
##### 理论依据
假设G=(V,E)是一个连通网，U是顶点集V的一个非空子集。若(u,v)是一条具有最小权值(代价)的边,其中u∈U，v∈V-U，则必存在一棵包含边(u,v)的最小生成树。
##### 示例
>详细图解见课件

![[Pasted image 20231108210402.png]]
(1) U={a}, V\U={b,c,d,e,f,g}，T={}。
	连接U和V\U之间最短的边是ab或ag任选一个，选ab
	T={ab},U={a,b},V\U={c,d,e,f,g}
(2) T={ab},U={a,b},V\U={c,d,e,f,g}
	连接U和V\U之间最短的边是bc
	T={ab,bc},U={a,b,c},V\U={d,e,f,g}
(3) T={ab,bc},U={a,b,c},V\U={d,e,f,g}
	连接U和V\U之间最短的边是ag
	T={ab,bc,ag},U={a,b,c,g},V\U={d,e,f}
(4) T={ab,bc,ag},U={a,b,c,g},V\U={d,e,f}
	连接U和V\U之间最短的边是gd或gf任选一个，选gd
	T={ab,bc,ag,gd},U={a,b,c,g,d},V\U={e,f}
(5) T={ab,bc,ag,gd},U={a,b,c,g,d},V\U={e,f}
	连接U和V\U之间最短的边是de
	T={ab,bc,ag,gd,de},U={a,b,c,g,d,e},V\U={f} 
(6) T={ab,bc,ag,gd,de},U={a,b,c,g,d,e},V\U={f}
	连接U和V\U之间最短的边是ef
	T={ab,bc,ag,gd,de,ef},U={a,b,c,g,d,e,f},V\U={} 
(7) T={ab,bc,ag,gd,de,ef}

##### 思想
设G=(V,E)是连通网，用T来记录G上最小生成树边的集合。
(1)算法开始时，U={1|1∈V}(选一个初始顶点)， T={ }；
(2)找到满足min{weight(u,v)|u∈U,v∈V-U}的边(ui ,vi )，把它并入集合T中，同时vi并入U；
(3)反复执行(2)，直到U=V时终止算法。

##### 算法
> 详细演示见课件7.4.1，p85
```c
void MiniSpanTree_P(MGraph G, vertextype u)
{
	k = Locatevex (G, u ); //k是节点u的编号
	for ( j=0; j<G.vexnum; ++j ) // 辅助数组初始化
		if (j!=k)
			closedge[j] = { u, G.arcs[k][j].adj };
		closedge[k].lowcost = 0; // 初始，U＝{u}
		for (i=1; i<G.vexnum; ++i)
		{
			k = minimum(closedge); // 求出加入生成树的下一个顶点(k)
			printf(closedge[k].adjvex, G.vexs[k]); // 输出生成树上一条边
			closedge[k].lowcost = 0; // 第k顶点并入U集
			for (j=0; j<G.vexnum; ++j) //修改临时最小权
				if (G.arcs[k][j].adj < closedge[j].lowcost)//&&G.arcs[k][j].adj !=0
					closedge[j] = ( G.vexs[k], G.arcs[k][j].adj );
		}
}
```

##### 算法分析
时间复杂性主要体现在两层循环上，复杂性是O(n<sup>2</sup>)
空间复杂性主要体现在一个辅助数组，复杂性是O(n)

#### Kruskal算法
##### 理论依据
设G=(V,E)是一个连通网, {(V1 ,E1 ),(V2 ,E2 ),...,(Vk ,Ek )}是G的生成森林,k>1,F= ,如果e=uv是E\F中权值最小的边且u∈Vi ,v$\notin$Vi中,则存在G的一个包含F∪{e}的生成树T,使得T的权不大于任一包含F的生成树的权。
##### 示例
![[Pasted image 20231108212053.png]]

##### 思想
设G=(V,E)是连通网，用T来记录G上最小生成树边的集合。
(1)从G中取最短边e，如果边e所关联的两个顶点不在T的同一个连通分量中，则将该边加入T；
(2)从G中删除边e； 
(3)重复(1)和(2)两个步骤，直到T中有n-1条边。

##### 算法概要
```c
输入:连通图G=(V,E),其中V={1,2,…,n}, 
输出:一棵最小生成树: 
void Kruskal(V,T)
{
	T=V;
	ncomp=n;//连通分量
	while(ncomp > 1)
	{
		从E中取出和删除权最小的边(v,u);
		if(v和u属于T中不同的连通分量)
			{
			T=T∪{(v,u)};
			ncomp--;
			}
	}
}
```
### Prim算法和Kruskal算法比较
| 算法名      | 普里姆算法 | 克鲁斯卡尔算法 |  
| :-----: | :-----: |  :--------: |
| 时间复杂度 | O(n<sup>2</sup>) | O(eloge) |
| 适用范围 | 稠密图 | 稀疏图 |
克鲁斯卡尔算法一般先按边权重对边进行排序，对于e条边来说，排序的最好时间复杂度是O(eloge)
## 关节点和重连通分量*

# #课件7-3  7.5 有向无环图的应用
---
### 背景
一个无环的有向图称作有向无环图，简称DAG（Directed Acyclic Graph）图。 
DAG图在工程计划和管理方面应用广泛。
工程(project)可分为若干个称作“活动”的子工程，并且这些子工程之间通常受着一定条件的约束。
某些子工程必须在另外的一些子工程完成之后才能开始。对整个工程和系统，人们主要关心的是两方面的问题：
	（1）工程能否顺利进行；
		——拓扑排序
	（2） 完成整个工程所必须的最短时间。
		——关键路径

## 拓扑排序
### 实例分析
学校课程的次序安排就是一个简单的工程，每一门课程的学习都是整个工程的活动。
有些课程需要在另外一些课程前安排，如何安排好课程学习的先后次序，这是一个典型的拓扑排序问题。
![[Pasted image 20231109103806.png]]

### 相关概念
#### 顶点活动网(AOV网，Activity On Vertex NetWork)
将顶点表示活动，边表示活动之间的关系的网称为顶点活动网
#### 拓扑序列
把AOV网中的所有顶点排成一个线性序列，该序列满足如下条件：若AOV网中存在从vi到vj的路径，则在该序列中，vi必位于vj之前
#### 拓扑排序
构造AOV网的拓扑序列的操作被称为拓扑排序

### 拓扑排序特点
(1)一个有向图的拓扑序列不一定唯一; 
(2)有向无环图一定存在拓扑序列; 
(3)有向有环图不存在拓扑序列; 
(4)通过构造拓扑序列，可判定AOV网是否存在环。
![[Pasted image 20231109104032.png]]

### 算法
#### 思想
(1)在有向图中选一个入度为0的顶点输出；
(2)从图中删除该顶点及所有它的出边；
(3)重复执行(1)和(2)，直到全部顶点均已输出，或图中剩余顶点的入度均不为0(说明图中存在回路，无法继续拓扑排序)。
![[Pasted image 20231109104149.png]]
#### 概要
```c
增加一个存放各顶点入度的数组indegree[] 
(1)扫描indegree[],将入度为零的顶点入栈; 
(2)while(栈非空)
	{
		弹出栈顶元素vi并输出;
		检查vi的出边表,将每条出边的终点vj的入度减1;
		若vj的入度减至0,则vj入栈;
	}
(3)若输出的顶点数小于n,则“有回路”;否则正常结束。
```
#### 详解
```c
void FindInDegree(ALGraph G, int indegree[]) // 求各顶点入度
{
	int i; ArcNode *p;
	for (i=0;i<G.vexnum;i++)
	{
		p=G.Vertices[i].firstarc;
		while (p) {
			indegree[p->adjvex]++;
			p=p->next;
		}
	}
}

Status TopologicalSort(ALGraph G) // 拓扑排序
{
	SqStack S;
	int count,k,i;
	ArcNode *p;
	int indegree[MAX_VERTEX_NUM];
	FindInDegree(G, indegree); // 对各顶点求入度
	InitStack(S); // 用来存储入度为零的顶点
	for (i=0; i<G.vexnum; ++i)
		if (!indegree[i]) Push(S, i); // 入度为0者进栈
	count = 0; // 计数器：对输出顶点计数
	while (!StackEmpty(S)) {
		Pop(S, i); // 出栈
		printf(i, G.Vertices[i].data); // 输出
		++count; // 计数
		for (p=G.Vertices[i].firstarc; p; p=p->nextarc) // 扫描顶点i的边链表
		{
			k = p->adjvex;
			if (!(--indegree[k])) Push(S, k); //更新入度，0度入栈
		}
	}
	if (count<G.vexnum) return ERROR;
	else return OK;
}
```
#### 算法分析
设AOV网有n个顶点，e条边。
初始建立入度为0的顶点栈，要检查所有顶点一次，执行时间为O(n)；排序中，若AOV网无回路，则每个顶点入、出栈各一次，每个边表结点被检查一次，执行时间为O(n+e)；
拓扑排序算法的时间复杂度为O(n+e)。
如果采用邻接矩阵存储结构，则时间复杂度为O(n<sup>2</sup>)。

#### 课前题
![](1@files_231025_143832_147%201.jpg)

## 关键路径
### 相关概念
#### AOE（Activity On Edge）网
带权的有向图，顶点表示事件，边表示活动，权表示活动持续的时间。
AOE网的特点
	表示实际工程计划的AOE网应该是无回路的; 
	只有一个入度为零的顶点(称作源点),表示整个活动开始; 
	只有一个出度为零的顶点(称作汇点)表示整个活动结束。
工程完工的最短时间是从源点到汇点的最长路径长度。
最长路径上的活动是影响工程进度的关键。
![[Pasted image 20231109105634.png]]
#### 关键路径
长度最长的路径称为关键路径。在AOE网中，有些活动可以同时进行。
#### 关键活动
关键路径上的活动都是关键活动。
关键活动延长必然影响工期
关键活动缩短不一定缩短工期
#### 事件的最早/最迟发生时间
事件vi的最早发生时间是从源点v1到vi的最长路径长度，记作ve(i)； (vertex_early)
	（用源点计算）
事件vk的最迟发生时间是vn的最早发生时间ve(n)减去vk到vn的最长路径长度，记作 vl(k)。
	（用汇点计算）
注：事件Vn的最早发生时间和最迟发生时间相等
#### 活动的最早/最迟发生时间
活动<vi,vj>的最早开始时间=vi的最早开始时间,记作ee(i);  （edge_early)
活动<vi,vj>的最迟开始时间=vj的最迟开始时间-<vi,vj>的持续时间,记作el(i)
我们的目的：就是要找出关键活动，为计算工期提供帮助。
关键活动的特点：其最早发生时间与最迟发生时间相等。

### 求关键活动算法要点
(1)求出每个事件i的最早发生时间ve(i)和最晚发生时间vl(i)
(2)根据(1)计算每个活动的最早发生时间和最迟发生时间
	设活动ai所对应的边为<j,k>,dut(<j,k>)表示其持续时间，活动ai的最早发生时间ee(i)和最迟发生时间el(i)分别为: 
		ee(i)=ve[j]即 ee(<j,k>)= ve[j]
		el(i)=vl[k]-dut(<j,k>)即 el(<j,k>)=vl[k]-dut(<j,k>)
(3)找出关键活动 比较ee(i)和el(i)，ee(i)和el(i)相等的活动即为关键活动。

### 求事件的最早发生时间和最晚发生时间步骤
第一步为前进阶段:
	从ve[1]=0开始,沿着路径上每条边的方向,用如下公式求每个事件的最早发生时间:
		ve[j]= Max{ve[i]+ dut(<i, j>)}
第二步为回退阶段:
	从已求出的vl[n]=ve[n]开始,沿着路径上每条边的相反方向,用如下公式求每个事件的最迟发生时间:
		vl[i]= Min{vl(j) − dut(<i, j>)}

### 关键路径算法所用数据结构
```c
float ve[n],vl[n]; //记录事件的最早发生时间和最晚发生时间

//邻接表的存储表示
#define MAX_VERTEX_NUM 20
// (1)边表： 
typedef struct ArcNode {
	int adjvex; // 该弧所指向的顶点的位置
	struct ArcNode *nextarc; // 指向下一条弧的指针
	InfoType *info; // 存权值
} ArcNode;

// (2)顶点表：
typedef struct vnode {
	vertextype data; // 顶点信息
	ArcNode *firstarc; // 指向第一条依附该顶点的弧
} VNode, AdjList[MAX_VERTEX_NUM];
typedef struct {
	AdjList vertices;
	int vexnum, arcnum;
	int kind; // 图的种类标志
} ALGraph;
ALGraph G;
```
### 关键迭代步骤演示
>见课件7.5.2.5 p120
### 算法详解
```c
// 求事件最早开始时间、拓扑逆序
status TopologicalOrder(ALGraph G, Stack &T)
{   // G为邻接表表示的有向网，T为拓扑逆序栈即T从栈顶到栈底为拓扑逆序
	// 求各顶点事件的最早发生时间ve(全局变量) 
	Stack S;
	int count=0,k;
	int indegree[vexnum],ve[G.vexnum]={0};
	ArcNode *p;
	InitStack(S); // 建入度为零的顶点栈S
	FindInDegree(G, indegree); //计算各个顶点的入度
	for (int j=0; j<G.vexnum; ++j)
		if (indegree[j]==0) Push(S, j); // 入度为0者进栈
	InitStack(T); //建拓扑逆序顶点栈T
	count = 0;
	while (!StackEmpty(S)) {
		Pop(S, j);Push(T, j);++count;
		for (p=G.vertices[j].firstarc; p; p=p->nextarc) {
			k = p->adjvex;
			if (--indegree[k] == 0) Push(S, k); // 若入度减为0，则入栈
			if (ve[j]+*(p->info) > ve[k])
				ve[k] = ve[j]+*(p->info);//更新事件k的最早发生时间
		}
	}
	if (count<G.vexnum) return ERROR; // 若G无回路，return OK，否则return ERROR
	else return OK;
} // TopologicalOrder

// 求事件最迟开始时间、关键活动
Status CriticalPath(ALGraph G)
{ // 输出有向网G的各项关键活动
	Stack T;
	int a,j,k,el,ee,dut;
	char tag;
	ArcNode *p;
	if (!TopologicalOrder(G, T)) return ERROR;
	for(a=0; a<G.vexnum; a++)
		vl[a] = ve[G.vexnum-1]; // 初始化顶点事件的最迟发生时间
	while (!StackEmpty(T)) // 按拓扑逆序求各顶点的vl值
		for (Pop(T, j), p=G.vertices[j].firstarc; p; p=p->nextarc) {
			k=p->adjvex; dut=*(p->info); //dut<j,k>
			if (vl[k]-dut < vl[j])
				vl[j] = vl[k]-dut; //更新事件k的最迟发生时间
		}
		for (j=0; j<G.vexnum; ++j) // 求ee,el和关键活动
			for (p=G.vertices[j].firstarc; p ; p=p->nextarc) {
				k=p->adjvex;
				dut=*(p->info);
				ee = ve[j];
				el = vl[k]-dut;
				tag = (ee==el) ? '*' : ' ';
				printf(j, k, dut, ee, el, tag); // 输出关键活动
			}
		return OK;
} // CriticalPath
```
### 算法分析
在拓扑排序求Ve[i]和逆拓扑有序求Vl[i]时，所需时间为O(n+e)；
求各个活动的e[k]和l[k]时所需时间为O(e)；
总共花费时间仍然是O(n+e)。
如果采用邻接矩阵存储结构，时间复杂性是O(n<sup>2</sup>)

# #课件7-4 7.6 最短路径
---
## 相关概念
路径长度：路径上边的权值之和
最短路径：两结点间权值之和最小的路径

## 单源最短路径（Dijkstra算法）
单源最短路径：给定有向图G和源点vi，求vi到G中其余各顶点的最短路径。

### 算法思想
![[Pasted image 20231109150851.png]]
![[Pasted image 20231109150914.png]]

### 算法
#### 概要
(1)初始化
	引入一个辅助数组D[ ], D[i]表示当前所找到的源点到每个终点i的最短路径长度。
		最短路径长度的初值：D[i]=`G.arcs[v0][i]`
	引入辅助数组final[ ],final[i]=1表示顶点i的最短路径已求出，final[i]=0表示顶点i的最短路径还没有求出。
		初始状态：final[v0] (源点)标志为1，其它为0。
	引入数组P[ ]来记录路径。
		P[i]中存放的是从源点到点i的当前最短路径上离i最近的那个点的编号,if P[i]=-1表示当前从源点到i没有最短路
(2)选择D[ ]中路径最小值的顶点v(已求出最短路的顶点除外)， v就是当前求得的一条最短路径的终点。 修改final[v]=1。
(3)修改未求出最短路径的顶点的最短路径长度
```c
if(D[v]+G.arcs[v][w]<D[w]){
	D[w]=D[v]+G.arcs[v][w]
	P[w]=v;
}
```
(4)重复操作(2)、(3) n-1次。求得从v0到图上其余各顶点的最短路径长度递增序列。
#### 详解
```c
void ShortestPath_DIJ(MGraph G, int v0, int &P, float &D) {
	int i=0, j, v, w, min, final[MAX_VERTEX_NUM];
	for (v=0; v<G.vexnum; ++v) {
		final[v] = FALSE;
		D[v] = G.arcs[v0][v];
		P[v] = -1; // 设空路径
		if (D[v] < INFINITY)
			P[v] = v0;
	}
	final[v0] = TRUE;
	P[v0]=-1
	// 初始化^
	for (i=1; i<G.vexnum; ++i) {
		min = INFINITY; // 当前所知离v0顶点的最近距离
		for (w=0; w<G.vexnum; ++w)
			if (!final[w]) // w顶点在V-S中
				if (D[w]<min) { v = w; min = D[w]; }
		final[v] = TRUE; //离v0顶点最近的v加入S集
		for (w=0; w<G.vexnum; ++w)
			if (!final[w] && (min+G.arcs[v][w]<D[w])) {
				D[w] = min + G.arcs[v][w].adj; P[w] = v; }
	} // for
} // ShortestPath_DIJ
	// 计算最短路径^
	
// 输出v0到各顶点的最小距离值和最短路径的逆序
for (i=0;i<G.vexnum;i++) {
	printf(“%f\n%d”,D[i],i);
	pre=p[i];
	while(pre!=-1) {
		printf(“%d”,pre);
		pre=p[pre];
	}//end of while
}//end of for
	// 输出最短路径^
```
### 算法分析
Dijkstra算法的时间复杂性主要体现在求每个顶点的最短路径时，需要修改距离值和求最小值，时间复杂性O(n<sup>2</sup>)
Dijkstra算法的空间复杂性主要体现在两个辅助数组，空间复杂性是O(n)。

### 算法功能
(1) 求一个顶点到其余各顶点的最短路求各顶点之间的最短路。 
(2) 判断任意两顶点i, j之间是否有路 
(3) 判断有没有包括任意两顶点i, j的环路。
![](Pasted%20image%2020231115170950.png)

## 每一对顶点之间的最短路径（Floyd算法）
依次把有向网络的每个顶点作为源点，重复执行Dijkstra算法n次，即可求得每对顶点之间的最短路径。 
而Floyd算法能直接求出所有顶点之间的最短路径

### 算法思想
对顶点进行编号，设顶点为 0, 1, ... , n-1，算法采用邻接矩阵`G.arcs[n][n]`表示有向网络。
(1) `D^(-1) [n][n]`表示中间不经过任何点的最短路径；`G.arcs[n][n]`本身； 
(2) `D^(0) [n][n]`中间只允许经过“0”号顶点的最短路径；(在D^(-1)基础上插入“0”) 
(3) `D^(1) [n][n]`中间只允许经过“0”号和“1”号的最短路径；(在D^(0)基础上插入“1”) 
……………………………………………………………………………………(在D(k-1)基础上插入“k”) 
(n-1) `D^(n-1) [n][n]`中间可经过所有顶点的最短路径。 (在D^(n-2)基础上插入“n-1”)

### 算法要点——迭代
设`D^(k) [n][n]`,中间只允许经过0,...k-1,k的最短路径已经求出； 
求`D^(k+1) [n][n]`，中间只允许经过0,...k-1,k,k+1的最短路径； 
`D^(k+1) [i][j]`是i与j间只可能经过1,2,…,k+1的最短路径。 
如果i与j经过k+1,则可以写成如下形式： 
	i……k+1……j 
	`D^(k) [i][k+1]+D^(k) [k+1][j]`
比较`D^(k) [i][k+1]+D(k) [k+1][j]`与`D^(k) [i][j]`的大小
把小值写进`D^(k+1) [i][j]`。
![](Pasted%20image%2020231115171937.png)

### 算法详解
```c
void ShortestPath_FLOYD(MGraph G,PathMatrix P[], DistancMatrix &D) {
	int v,w,u,i;
	for(v=0; v<G.vexnum; ++v)
		for(w=0; w<G.vexnum;++w) { // 初始化
			D[v][w] = G.arcs[v][w];
			for (u=0; u<G.vexnum; ++u)
				P[v][w][u] = FALSE;
				if (D[v][w] < INFINITY)
					P[v][w][v] = P[v][w][w] = TRUE;
					//P是路径矩阵P[x][y][z]的含义是从x到y是否经过z，如果是值为1，否则为0
		}
	for (u=0; u<G.vexnum; ++u) //经过的顶点
		for (v=0; v<G.vexnum; ++v)
			for (w=0; w<G.vexnum; ++w)
				if (D[v][u]+D[u][w] < D[v][w]) { // 从v经u到w的一条路径更短
					D[v][w] = D[v][u]+D[u][w];
					for (i=0; i<G.vexnum; ++i)
					P[v][w][i] =(P[v][u][i] || P[u][w][i]);
				}
}
```
### 算法分析
Floyd算法的时间复杂性主要体现在三重循环， 时间复杂性为O(n<sup>3</sup>) 
Floyd算法的空间复杂性主要体现在利用了一个二维数组，空间复杂性为O(n<sup>2</sup>)

权值可以是负值，但不能有长度是负值的环路。
### 算法功能
(1) 求出了任意两点i,j之间的最短路径。 
(2) 可以判断任意两点之间是否有路。 
	可以判断是不是强连通图。 
(3) 可以判断有向图经过任意两点是否有环路。

# 本章小结
---
(1) 熟练掌握图的定义、术语和性质； 
(2) 熟练掌握图的邻接矩阵、邻接表的存储结构 及其构造方法； 
(3) 熟练掌握图的两种遍历方法：深度优先遍历和广度优先遍历； 
(4) 掌握最小生成树的算法、拓扑排序的算法； 
(5) 理解关键路径的算法，构造最短路径的Dijkstra算法和Floyd算法。