# BFS 广度优先搜索

> BFS(breadth-first search)，广度优先搜索，和深度优先搜索（DFS,depth-first search）相对应，是图最常用的两种遍历方法
>
> BFS的策略是一层一层的扫描，一层搜完，再继续搜索下一层，一般使用**队列**来实现，同时配合迭代
>
> DFS的策略是一条路走到黑，找不到就回到上一层，从其他未搜索的路径出发，继续往下搜，一般使用**递归**实现，当然也可以使用**栈**来实现

## BFS模板

```java
// 计算从起点 start 到终点 target 的最近距离
int BFS(Node start, Node target) {
    Queue<Node> q; // 核心数据结构
    Set<Node> visited; // 避免走回头路
    
    q.offer(start); // 将起点加入队列
    visited.add(start);
    int step = 0; // 记录扩散的步数

    while (q not empty) {
        // 直接使用q.size()记录每层的节点数
        // 比我自己摸索的使用 null z
        int sz = q.size();
        /* 将当前队列中的所有节点向四周扩散 */
        for (int i = 0; i < sz; i++) {
            Node cur = q.poll();
            /* 划重点：这里判断是否到达终点 */
            if (cur is target)
                return step;
            /* 将 cur 的相邻节点加入队列 */
            for (Node x : cur.adj())
                if (x not in visited) {
                    q.offer(x);
                    visited.add(x);
                }
        }
        /* 划重点：更新步数在这里 */
        step++;
    }
}
```

