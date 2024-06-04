## [994. 腐烂的橘子](https://leetcode.cn/problems/rotting-oranges/)

在给定的 `m x n` 网格 `grid` 中，每个单元格可以有以下三个值之一：

- 值 `0` 代表空单元格；
- 值 `1` 代表新鲜橘子；
- 值 `2` 代表腐烂的橘子。

每分钟，腐烂的橘子 **周围 4 个方向上相邻** 的新鲜橘子都会腐烂。

返回 *直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 `-1`* 。

 

**示例 1：**

**![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/16/oranges.png)**

```
输入：grid = [[2,1,1],[1,1,0],[0,1,1]]
输出：4
```

**示例 2：**

```
输入：grid = [[2,1,1],[0,1,1],[1,0,1]]
输出：-1
解释：左下角的橘子（第 2 行， 第 0 列）永远不会腐烂，因为腐烂只会发生在 4 个方向上。
```

**示例 3：**

```
输入：grid = [[0,2]]
输出：0
解释：因为 0 分钟时已经没有新鲜橘子了，所以答案就是 0 。
```

 

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 10`
- `grid[i][j]` 仅为 `0`、`1` 或 `2`
**思路：**
$bfs$广度优先遍历
首先统计矩阵中$fresh$橘子的个数以及腐烂橘子的位置放在列表$q$中。
然后按照规则进行$bfs$即在$q$列表中储存当前分钟新腐烂的橘子
然后再按照4个方向的腐烂规则进行遍历。直到$q$中的数据全部为0
此时如果$fresh$的值不为0则证明无法腐烂所有橘子
```python
class Solution:
    def orangesRotting(self, grid: List[List[int]]) -> int:
        m = len(grid)
        n = len(grid[0])
        fresh = 0
        q = []
        for i,row in enumerate(grid):
            for j,x in enumerate(row):
                if x == 1:
                    fresh +=1
                elif x == 2:
                    q.append((i,j))
        ans = -1
        while q:
            ans += 1
            tmp = q
            q = []
            for x,y in tmp:
                for i,j in (x-1,y),(x+1,y),(x,y-1),(x,y+1):
                    if 0<=i<m and 0<=j<n and grid[i][j] ==1:
                        fresh-=1
                        grid[i][j]=2
                        q.append((i,j))
        return -1 if fresh else max(ans,0)
```

##  [3067. 在带权树网络中统计可连接服务器对数目](https://leetcode.cn/problems/count-pairs-of-connectable-servers-in-a-weighted-tree-network/)

给你一棵无根带权树，树中总共有 `n` 个节点，分别表示 `n` 个服务器，服务器从 `0` 到 `n - 1` 编号。同时给你一个数组 `edges` ，其中 `edges[i] = [ai, bi, weighti]` 表示节点 `ai` 和 `bi` 之间有一条双向边，边的权值为 `weighti` 。再给你一个整数 `signalSpeed` 。

如果两个服务器 `a` ，`b` 和 `c` 满足以下条件，那么我们称服务器 `a` 和 `b` 是通过服务器 `c` **可连接的** ：

- `a < b` ，`a != c` 且 `b != c` 。
- 从 `c` 到 `a` 的距离是可以被 `signalSpeed` 整除的。
- 从 `c` 到 `b` 的距离是可以被 `signalSpeed` 整除的。
- 从 `c` 到 `b` 的路径与从 `c` 到 `a` 的路径没有任何公共边。

请你返回一个长度为 `n` 的整数数组 `count` ，其中 `count[i]` 表示通过服务器 `i` **可连接** 的服务器对的 **数目** 。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2024/01/21/example22.png)

```
输入：edges = [[0,1,1],[1,2,5],[2,3,13],[3,4,9],[4,5,2]], signalSpeed = 1
输出：[0,4,6,6,4,0]
解释：由于 signalSpeed 等于 1 ，count[c] 等于所有从 c 开始且没有公共边的路径对数目。
在输入图中，count[c] 等于服务器 c 左边服务器数目乘以右边服务器数目。
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2024/01/21/example11.png)

```
输入：edges = [[0,6,3],[6,5,3],[0,3,1],[3,2,7],[3,1,6],[3,4,2]], signalSpeed = 3
输出：[2,0,0,0,0,0,2]
解释：通过服务器 0 ，有 2 个可连接服务器对(4, 5) 和 (4, 6) 。
通过服务器 6 ，有 2 个可连接服务器对 (4, 5) 和 (0, 5) 。
所有服务器对都必须通过服务器 0 或 6 才可连接，所以其他服务器对应的可连接服务器对数目都为 0 。
```

 

**提示：**

- `2 <= n <= 1000`

- `edges.length == n - 1`

- `edges[i].length == 3`

- `0 <= ai, bi < n`

- `edges[i] = [ai, bi, weighti]`

- `1 <= weighti <= 106`

- `1 <= signalSpeed <= 106`

- 输入保证 `edges` 构成一棵合法的树。

**思路：**

- 根据树的定义，构建一个包含每一个节点的$list[list]$数据结构。下标$i$代表每一个节点$list[i]$代表与这个节点相联系的边。

- 由于一对节点路径不能重复所以我们只需要统计获得$list[i]$中不同边中满足	$edge/signalSpeed==0$节点的数量$n_i$

- 根据递推公式$ans+=n_i*sum(n_j)$,$0<j<i$求出答案

```python
class Solution:
    def countPairsOfConnectableServers(self, edges: List[List[int]], signalSpeed: int) -> List[int]:
        n = len(edges)+1
        g = [[] for _ in range(n)]
        for x,y,wt in edges:
            g[x].append((y,wt))
            g[y].append((x,wt))
        def dfs(x:int,fa:int,s:int)->int:
            cnt = 0 if s % signalSpeed else 1
            for y,wt in g[x]:
                if y != fa:
                    cnt+=dfs(y,x,s+wt)
            return cnt
        ans = [0]*n
        for i,gi in enumerate(g):
            s=0
            for y,wt in gi:
                cnt = dfs(y,i,wt)
                ans[i] += cnt*s
                s += cnt
        return ans
```

