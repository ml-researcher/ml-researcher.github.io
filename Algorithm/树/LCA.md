# LCA

求LCA（最近公共祖先）的方法有很多，这里列举最常用的四种方法：暴力、倍增、树链剖分、离线。

## 暴力

先预处理出每个点的深度，假设要求u和v的LCA，就看谁的深度大，就让谁往上走，如果深度一样就一起往上走，直到两个点重合。代码非常简单直白：

```cpp
int lca(int u, int v)
{
	while (u != v) {
		if (dep[u] >= dep[v]) u = fa[u];
		else v = fa[v];
	}
	return u;
}
```

当然，为了过渡到下面的优化算法，把上面的过程分解成两个部分：第一步，先把u和v调整到深度相同；第二步，一起向上走，直到重合。代码类似于这样：

```cpp
int lca(int u, int v)
{
	if (dep[u] < dep[v]) swap(u, v); // 让u变成深的那个
	while (dep[u] > dep[v]) u = fa[u]; // 调整到深度相同
	while (u != v) u = fa[u], v = fa[v]; // 一起向上跳，直到重合
	return u;
}
```

## 在线算法

### 倍增

可以发现，上面的暴力算法时间复杂度主要耗费在了跳这个操作，每次只能跳一格。那么用倍增的方法，就可以做到让跳的次数到达log级别。

用到一种倍增的思想，记 fastfa[i][j] 表示从$$i$$向上跳$$2^j$$步会跳到谁，这个可以在$$O(n \log n)$$的时间复杂度内预处理。

初始化：$$fa[root]=root,\ fastfa[i][0]=fa[i]$$。
递推式：$$fastfa[i][j]=fast[fastfa[i][j-1]][j-1]$$。

对于上面暴力的代码，调整深度，可以用fastfa数组在log次以内向上跳dep[u]-dep[v]步；对于一起向上跳的部分，可以用fastfa数组在log次以内跳到最下面的能让它们重合的位置。代码如下：

```cpp
int jump(int u, int k) // 从u向上跳k步
{
	int i = 0;
	while (k) {
		if (k & 1) u = fastfa[u][i];
		k >>= 1;
		i ++;
	}
	return u;
}

int jump_together(int u, int v) // u和v一起向上跳，u和v深度相同
{
	if (u == v) return u;
	int ma = 0;
	while (fastfa[u][ma] != fastfa[v][ma]) ma ++;
	for (int i=ma-1; i>=0; i--) {
		if (fastfa[u][i] != fastfa[v][i]) {
			u = fastfa[u][i];
			v = fastfa[v][i];
		}
	}
	return fa[u];
}

int lca(int u, int v) // 三步与暴力算法对应
{
	if (dep[u] < dep[v]) swap(u, v);
	u = jump(u, dep[u]-dep[v]);
	return jump_together(u, v);
}
```

### 树链剖分

树链剖分实际上就是这篇最开始的第一版暴力的改进。假设我们现在已经把树剖分好了，看一下如何在求两个点的LCA。

还是先分析一下之前的暴力代码：

```cpp
int lca(int u, int v)
{
	while (u != v) {
		if (dep[u] >= dep[v]) u = fa[u];
		else v = fa[v];
	}
	return u;
}
```

我们看谁更深，就让谁往上跳，但是实际上这里我们并不是说谁更深，谁就往上跳，而是谁跳完了以后更深，谁就往上跳。这两个看似没有区别，但是在树链剖分里面，有时候会一次跳多格（跳重链），效果就很不一样了。所以实现起来是这样的：

```cpp
int lca(int u, int v)
{
	while (u != v) {
		if (same_heavy_link(u, v)) return select_shallow(u, v); // 如果在同一条重链，直接返回浅的那个
		if (dep[jump(u)] >= dep[jump(v)]) u = jump(u);
		else v = jump(v); // jump的功能是跳重链或者跳轻边
	}
	return u;
}
```

## 离线算法

任何优化算法其实都可以从暴力算法里找到影子。上面的在线算法的改进就很显然，其实暴力算法还有另一种写法，就是先把一个节点的所有祖先都打上标记，然后让另一个节点往上走，遇到的第一个标记的节点就是LCA。

```cpp
int color[maxn];
int tot_col; // 为避免每次memset color数组，记录一个tot
int lca(int u, int v)
{
	tot_col ++;
	while (fa[u] != u) { // 标记u和u的所有祖先
		color[u] = tot_col;
		u = fa[u];
	}
	color[u] = tot_col;
	while (color[v] != tot_col) v = fa[v]; // 找到第一个标记了的祖先
	return v;
}
```

这种暴力方式的优化，就产生了离线的LCA求法。

首先，把所有的询问(u, v)变成两个询问(u, v)和(v, u)，然后把所有(i, \*)的询问挂在i节点上。然后对树做一次dfs。

初始所有点都是白色，在dfs的过程中，正在遍历的点被标记成红色，遍历完回溯的点标记成蓝色。当dfs到一个点u的时候，遍历一次以它开头的查询，如果另一个点v是白色，就忽略这个查询；如果v是红色，它们的LCA显然就是v；如果v是蓝色，它们的LCA就是v向上走遇到的第一个红色点。

每个蓝色点向上走，遇到的第一个红色是谁，这个可以用在dfs的过程中用并查集来维护。方法就是每当dfs完点u，准备回溯的时候（也是u被标记成蓝色的时候），就merge(u, fa[u])。

## 练习

离线算法在询问很多的情况下确实优势明显，比如[这个题](https://nanti.jisuanke.com/t/17114)不用离线就超时。（做这个题之前要做好心理准备，建议先自己想一下，实在想不出来再点击下面的按钮查看思路）
<details>
首先，位运算加速矩阵乘法
其次，用离线LCA得到所有查询的最近公共祖先，在维护并查集的过程中顺便维护一下与祖先的路径乘积（两个方向都要维护一下）

更详细解释：http://izard.space/?p=153

这个题目更本质的问题是：不用减法如何求路径和？之前求路径和通常都是dis(u, v) = dis(u) + dis(v) - 2 * dis(lca)，做了这个题以后，可以发现，减法是不必要的，尤其是在没有逆元运算的时候（矩阵乘法），只能用这里的方法。
</details>

* [AC代码](http://lvqingsong.info/pastebin/view/d8372d16)：吐槽——这道题卡常太严重了，稍微多运算一点就会被卡

## 参考资料

* http://www.gonglin91.com/lca_grated/
* https://riteme.github.io/blog/2017-9-3/lca.html
* https://riteme.github.io/blog/2016-2-1/lca.html
* https://segmentfault.com/a/1190000015145319

## 拓展

基本不咋用：

* [欧拉序+RMQ求LCA](https://blog.csdn.net/cold_chair/article/details/71249622)
* [其他求LCA的方法](https://www.zhihu.com/question/19957473/answer/119447963)