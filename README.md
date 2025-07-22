

# 树和图的存储

邻接表存储

```cpp
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 100010, M = N * 2;

int h[N], e[M], ne[M], idx;

void add(int a, int b) {
    e[idx] = b; // 存储边的终点
    ne[idx] = h[a]; // 存储边的下一个节点为原链表头
    h[a] = idx++; // 更新链表头为当前边的索引，并增加索引
}

int main()
{
    memset(h, -1, sizeof h);
}
```

- `h[N]`：头节点数组，存储每个节点对应的链表头指针。
- `e[M]`：边的终点数组，存储每条边的目标节点。
- `ne[M]`：下一条边数组，存储链表的下一个节点索引。
- `idx`：全局计数器，记录当前边的编号。

# ![搜索与图论-6](https://github.com/xuegao2005/Trees_and_graphs/blob/main/%E6%90%9C%E7%B4%A2%E4%B8%8E%E5%9B%BE%E8%AE%BA-6.jpg)



# 树和图的遍历

图的DFS

```cpp
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 100010, M = N * 2; 

int n, m;
int h[N], e[M], ne[M], idx; 
bool st[N];

void add(int a, int b) 
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

void dfs(int u) // 从第u个节点开始深度优先搜索
{
    st[u] = true; // 标记第u节点已经被搜过了

    // 遍历u的所有出边:
    // 1.通过 h[u] 获取节点 u 的第一条边 
    // 2.通过 ne[i] 获取下一条边 
    // 3.直到没有更多的边结束循环
    for (int i = h[u]; i != -1; i = ne[i]) { 
        int j = e[i]; // e[i]存储的是第i条边的终点节点编号，因此j就是u的一个邻居节点
        if (!st[j]) dfs(j);// 如果j没有被搜过, 就从j开始搜索
    }
}

int main()
{
    memset(h, -1, sizeof h); 

    dfs(1);
}
```

树的重心

![image-20250603163534096](../assets/image-20250603163534096.png)

```cpp
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 100010, M = N * 2; 

int n;
int h[N], e[M], ne[M], idx; 
bool st[N];

int ans = N; // 最终答案为ans

void add(int a, int b) 
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

// 以u为根节点的树的点的数量
int dfs(int u) 
{
    st[u] = true; 

    int res = 0, sum = 1; // res是当前子树的最大点数，sum是当前子树的点的数量，u已经算一个点了
    for (int i = h[u]; i != -1; i = ne[i]) { // 遍历u的所有邻接点
        int j = e[i]; // 找到j是u的邻接点
        if (!st[j]) 
        {
            int s = dfs(j); // 以j为根节点的子树的点的数量，就是sum
            res = max(res, s);  // 更新res为当前子树的最大点数
            sum += s; // 当前子树的点的数量
        }
    }
    // 现在已经遍历完u的所有邻接点了，现在的res是包含u点的子树的最大点数
    res = max(res, n - sum); // 当前子树的总数和上面那一坨的总数 对比

    // 现在的res是以u为根节点的子树的最大点数
    ans = min(ans, res); // 与其他点为根节点的res对比，最小的那个就是最终答案
    return sum;
}

int main()
{
    cin >> n;
    memset(h, -1, sizeof h); 

    for (int i = 0; i < n - 1; i++) // n个节点有n - 1条边
    {
        int a, b;
        cin >> a >> b;
        add(a, b), add(b, a);
    }

    dfs(1); // 任意节点开始都可以

    cout << ans << endl;
    return 0;
}
```

图的BFS

![image-20250603191554129](../assets/image-20250603191554129.png)

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 100010;

int n, m;
int h[N], e[N], ne[N], idx;
int d[N], q[N]; // d[i]表示i号点的距离，q[i]是队列

void add(int a, int b)
{
  e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

int bfs()
{
  int hh = 0, tt = 0; // hh是队头，tt是队尾
  q[0] = 1; // 从1号点开始遍历

  memset(d, -1, sizeof d); // 初始化距离数组

  d[1] = 0; // 1号点的距离为0

  while(hh <=tt) // 当队列不为空时
  {
    int t = q[hh ++ ]; // 取出队头元素t

    // 遍历t的所有邻居
    for (int i = h[t]; i != -1; i = ne[i])
    {
      int j = e[i]; // j是t的一个邻居
      if (d[j] == -1) // 如果j还没有被访问过
      {
        d[j] = d[t] + 1; // 更新j的距离
        q[ ++ t] = j; // 把j加入队列
      }
    }
  }

  return d[n]; // 返回n号点的距离
}

int main()
{
  // 读入n个点和m条边
  cin >> n >> m;

  // 初始化邻接表
  memset(h, -1, sizeof h);

  for (int i = 0; i < m; i++) // 读入m条边
  {
    int a, b;
    cin >> a >> b;
    add(a, b), add(b, a);
  }

  cout << bfs() << endl;

  return 0;
}
```



有向图的拓扑序列

![image-20250603204342856](../assets/image-20250603204342856.png)

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 100010;

int n, m;
int h[N], e[N], ne[N], idx;
int d[N], q[N]; // d[i]表示i的入度, q[i]表示第i个节点

void add (int a, int b)
{
  e[idx] = b, ne[idx] = h[a], h[a] = idx ++ ;
}

bool topsort()
{
  int hh = 0, tt = -1;
  
  for (int i = 1; i <= n; i ++ )
    if (!d[i]) // 入度为0的节点入队
      q[ ++ tt] = i;

  while (hh <= tt)
  {
    int t = q[hh ++ ]; // 出队一个节点

    for (int i = h[t]; i != -1; i = ne[i]) // 遍历t的所有邻接点
    {
      int j = e[i]; // j是t的一个邻接点
      d[j] -- ; // j的入度-1

      if (d[j] == 0) // 如果j的入度变为0, 则入队
        q[ ++ tt] = j;
    }
  }

  // 如果队列中有n个节点, 则有拓扑排序，且是一个有向无环图
  return tt == n - 1;
}

int main()
{
  cin >> n >> m;

  memset(h, -1, sizeof h);

  for (int i = 0; i < m; i ++ )
  {
    int a, b;
    cin >> a >> b;
    add(a, b);
    d[b] ++ ; // 插入一条a->b边, b的入度要+1
  }

  if (topsort())
  {
    for (int i = 0; i < n; i++ ) printf("%d ", q[i]); // 输出拓扑序(不是唯一的)
    puts("");
  }
  else puts("-1");

  return 0;
}
```

