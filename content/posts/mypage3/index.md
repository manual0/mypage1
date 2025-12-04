+++
title = "图论"
draft = false
date = "2025-05-09"
tags = ["学习","数据结构"]
+++

### 有向加权图（邻接表实现）

我这里给出一个简单的通用实现，后文图论算法教程和习题中可能会用到。其中有一些可以优化的点我写在注释中了。

```cpp
#include <iostream>
#include <vector>
#include <stdexcept>
using namespace std;

// 加权有向图的通用实现（邻接表）
class WeightedDigraph {
public:
    // 存储相邻节点及边的权重
    struct Edge {
        int to;
        int weight;

        Edge(int to, int weight) {
            this->to = to;
            this->weight = weight;
        }
    };

private:
    // 邻接表，graph[v] 存储节点 v 的所有邻居节点及对应权重
    vector<vector<Edge>> graph;

public:
    WeightedDigraph(int n) {
        // 我们这里简单起见，建图时要传入节点总数，这其实可以优化
        // 比如把 graph 设置为 Map<Integer, List<Edge>>，就可以动态添加新节点了
        graph = vector<vector<Edge>>(n);
    }

    // 增，添加一条带权重的有向边，复杂度 O(1)
    void addEdge(int from, int to, int weight) {
        graph[from].emplace_back(to, weight);
    }

    // 删，删除一条有向边，复杂度 O(V)
    void removeEdge(int from, int to) {
        for (auto it = graph[from].begin(); it != graph[from].end(); ++it) {
            if (it->to == to) {
                graph[from].erase(it);
                break;
            }
        }
    }

    // 查，判断两个节点是否相邻，复杂度 O(V)
    bool hasEdge(int from, int to) {
        for (const auto& e : graph[from]) {
            if (e.to == to) {
                return true;
            }
        }
        return false;
    }

    // 查，返回一条边的权重，复杂度 O(V)
    int weight(int from, int to) {
        for (const auto& e : graph[from]) {
            if (e.to == to) {
                return e.weight;
            }
        }
        throw invalid_argument("No such edge");
    }

    // 查，返回某个节点的所有邻居节点，复杂度 O(1)
    const vector<Edge>& neighbors(int v) {
        return graph[v];
    }
};

int main() {
    WeightedDigraph graph(3);
    graph.addEdge(0, 1, 1);
    graph.addEdge(1, 2, 2);
    graph.addEdge(2, 0, 3);
    graph.addEdge(2, 1, 4);

    cout << boolalpha << graph.hasEdge(0, 1) << endl; // true
    cout << boolalpha << graph.hasEdge(1, 0) << endl; // false

    for (const auto& edge : graph.neighbors(2)) {
        cout << "2 -> " << edge.to << ", wight: " << edge.weight << endl;
    }
    // 2 -> 0, wight: 3
    // 2 -> 1, wight: 4

    graph.removeEdge(0, 1);
    cout << boolalpha << graph.hasEdge(0, 1) << endl; // false

    return 0;
}
```

### 有向加权图（邻接矩阵实现）

没啥可说的，具体看代码和注释吧：

```cpp
int** matrix = new int*[m];  //new int(5) 或者 new int[5]() 或者 new int[3]{1,2,3}
for (int i = 0; i < m; i++) {
    matrix[i] = new int[n]();  // 分配并初始化为 0
}

// 使用示例
matrix[0][1] = 42;  // 访问元素

// 释放内存
for (int i = 0; i < m; ++i) {
    delete[] matrix[i];
}
delete[] matrix;
```

```cpp
#include <iostream>
#include <vector>
using namespace std;
// 加权有向图的通用实现（邻接矩阵）
class WeightedDigraph {
public:
    // 存储相邻节点及边的权重
    struct Edge {
        int to;
        int weight;

        Edge(int to, int weight) : to(to), weight(weight) {}
    };

    WeightedDigraph(int n) {
        matrix =  vector< vector<int>>(n,  vector<int>(n, 0));
    }

    // 增，添加一条带权重的有向边，复杂度 O(1)
    void addEdge(int from, int to, int weight) {
        matrix[from][to] = weight;
    }

    // 删，删除一条有向边，复杂度 O(1)
    void removeEdge(int from, int to) {
        matrix[from][to] = 0;
    }

    // 查，判断两个节点是否相邻，复杂度 O(1)
    bool hasEdge(int from, int to) {
        return matrix[from][to] != 0;
    }

    // 查，返回一条边的权重，复杂度 O(1)
    int weight(int from, int to) {
        return matrix[from][to];
    }

    // 查，返回某个节点的所有邻居节点，复杂度 O(V)
     vector<Edge> neighbors(int v) {
         vector<Edge> res;
        for (int i = 0; i < matrix[v].size(); i++) {
            if (matrix[v][i] > 0) {
                res.push_back(Edge(i, matrix[v][i]));
            }
        }
        return res;
    }

private:
    // 邻接矩阵，matrix[from][to] 存储从节点 from 到节点 to 的边的权重
    // 0 表示没有连接
     vector< vector<int>> matrix;
};

int main() {
    WeightedDigraph graph(3);
    graph.addEdge(0, 1, 1);
    graph.addEdge(1, 2, 2);
    graph.addEdge(2, 0, 3);
    graph.addEdge(2, 1, 4);

     cout <<  boolalpha;
     cout << graph.hasEdge(0, 1) <<  endl; // true
     cout << graph.hasEdge(1, 0) <<  endl; // false

    for (const auto& edge : graph.neighbors(2)) {
         cout << "2 -> " << edge.to << ", weight: " << edge.weight <<  endl;
    }
    // 2 -> 0, weight: 3
    // 2 -> 1, weight: 4

    graph.removeEdge(0, 1);
     cout << graph.hasEdge(0, 1) <<  endl; // false

    return 0;
}
```

### 有向无权图（邻接表/邻接矩阵实现）

直接复用上面的  `WeightedDigraph`  类就行，把  `addEdge`  方法的权重参数默认设置为 1 就行了。比较简单，我就不写代码了。

### 无向加权图（邻接表/邻接矩阵实现）

无向加权图就等同于双向的有向加权图，所以直接复用上面用邻接表/领接矩阵实现的  `WeightedDigraph`  类就行了，只是在增加边的时候，要同时添加两条边：

```cpp
// 无向加权图的通用实现
class WeightedUndigraph {
private:
    WeightedDigraph graph;

public:
    WeightedUndigraph(int n) : graph(n) {}

    // 增，添加一条带权重的无向边
    void addEdge(int from, int to, int weight) {
        graph.addEdge(from, to, weight);
        graph.addEdge(to, from, weight);
    }

    // 删，删除一条无向边
    void removeEdge(int from, int to) {
        graph.removeEdge(from, to);
        graph.removeEdge(to, from);
    }

    // 查，判断两个节点是否相邻
    bool hasEdge(int from, int to) {
        return graph.hasEdge(from, to);
    }

    // 查，返回一条边的权重
    int weight(int from, int to) {
        return graph.weight(from, to);
    }

    // 查，返回某个节点的所有邻居节点
    const  vector<WeightedDigraph::Edge>& neighbors(int v) const {
        return graph.neighbors(v);
    }
};

int main() {
    WeightedUndigraph graph(3);
    graph.addEdge(0, 1, 1);
    graph.addEdge(1, 2, 2);
    graph.addEdge(2, 0, 3);
    graph.addEdge(2, 1, 4);

     cout <<  boolalpha << graph.hasEdge(0, 1) <<  endl; // true
     cout <<  boolalpha << graph.hasEdge(1, 0) <<  endl; // true

    for (const auto& edge : graph.neighbors(2)) {
         cout << "2 <-> " << edge.to << ", weight: " << edge.weight <<  endl;
    }
    // 2 <-> 0, weight: 3
    // 2 <-> 1, weight: 4

    graph.removeEdge(0, 1);
     cout <<  boolalpha << graph.hasEdge(0, 1) <<  endl; // false
     cout <<  boolalpha << graph.hasEdge(1, 0) <<  endl; // false

    return 0;
}
```

### 无向无权图（邻接表/邻接矩阵实现）

直接复用上面的  `WeightedUndigraph`  类就行，把  `addEdge`  方法的权重参数默认设置为 1 就行了。比较简单，我就不写代码了。

## DFS 遍历

### 顶点遍历

```c++
// 遍历图的所有节点
void traverse(const Graph& graph, int s, vector<bool>& visited) {
    // base case
    if (s < 0 || s >= graph.size()) {
        return;
    }
    if (visited[s]) {
        // 防止死循环
        return;
    }
    // 前序位置
    visited[s] = true;
    cout << "visit " << s << endl;
    for (const Graph::Edge& e : graph.neighbors(s)) {
        traverse(graph, e.to, visited);
    }
    // 后序位置
}
```

### 边遍历

```cpp
// 下面的算法代码可以遍历图的所有路径，寻找从 src 到 dest 的所有路径

// onPath 和 path 记录当前递归路径上的节点
vector<bool> onPath(graph.size());
list<int> path;

void traverse(Graph& graph, int src, int dest) {
    // base case
    if (src < 0 || src >= graph.size()) {
        return;
    }
    if (onPath[src]) {
        // 防止死循环（成环）
        return;
    }
    // 前序位置
    onPath[src] = true;
    path.push_back(src);
    if (src == dest) {
        cout << "find path: ";
        for (int node : path) {
            cout << node << " ";
        }
        cout << endl;
    }
    for (const Edge& e : graph.neighbors(src)) {
        traverse(graph, e.to, dest);
    }
    // 后序位置
    path.pop_back();
    onPath[src] = false;
}
```

## BFS 遍历

### 写法一

第一种写法是不记录遍历步数的：

```cpp
// 多叉树的层序遍历
void levelOrderTraverse(Node* root) {
    if (root == nullptr) {
        return;
    }

    queue<Node*> q;
    q.push(root);

    while (!q.empty()) {
        Node* cur = q.front();
        q.pop();
        // 访问 cur 节点
        cout << cur->val << endl;

        // 把 cur 的所有子节点加入队列
        for (Node* child : cur->children) {
            q.push(child);
        }
    }
}


// 图结构的 BFS 遍历，从节点 s 开始进行 BFS
void bfs(const Graph& graph, int s) {
    vector<bool> visited(graph.size(), false);
    queue<int> q;
    q.push(s);
    visited[s] = true;

    while (!q.empty()) {
        int cur = q.front();
        q.pop();
        cout << "visit " << cur << endl;
        for (const Edge& e : graph.neighbors(cur)) {
            if (!visited[e.to]) {
                q.push(e.to);
                visited[e.to] = true;
            }
        }
    }
}
```

### 写法二

第二种能够记录遍历步数的写法：

```cpp
// 多叉树的层序遍历
void levelOrderTraverse(Node* root) {
    if (root == nullptr) {
        return;
    }
    queue<Node*> q;
    q.push(root);
    // 记录当前遍历到的层数（根节点视为第 1 层）
    int depth = 1;

    while (!q.empty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            Node* cur = q.front();
            q.pop();
            // 访问 cur 节点，同时知道它所在的层数
            cout << "depth = " << depth << ", val = " << cur->val << endl;

            for (Node* child : cur->children) {
                q.push(child);
            }
        }
        depth++;
    }
}


// 从 s 开始 BFS 遍历图的所有节点，且记录遍历的步数
void bfs(const Graph& graph, int s) {
    vector<bool> visited(graph.size(), false);
    queue<int> q;
    q.push(s);
    visited[s] = true;
    // 记录从 s 开始走到当前节点的步数
    int step = 0;
    while (!q.empty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            int cur = q.front();
            q.pop();
            cout << "visit " << cur << " at step " << step << endl;
            for (const Edge& e : graph.neighbors(cur)) {
                if (!visited[e.to]) {
                    q.push(e.to);
                    visited[e.to] = true;
                }
            }
        }
        step++;
    }
}
```

这个  `step`  变量记录了从起点  `s`  开始的遍历步数，对于无权图来说，相当于每条边的权重都是 1，这个变量就可以辅助我们判断最短路径。

### 写法三

第三种能够适配不同权重边的写法：

```cpp
// 多叉树的层序遍历
// 每个节点自行维护 State 类，记录深度等信息
class State {
public:
    Node* node;
    int depth;

    State(Node* node, int depth) : node(node), depth(depth) {}
};

void levelOrderTraverse(Node* root) {
    if (root == nullptr) {
        return;
    }
    queue<State> q;
    // 记录当前遍历到的层数（根节点视为第 1 层）
    q.push(State(root, 1));

    while (!q.empty()) {
        State state = q.front();
        q.pop();
        Node* cur = state.node;
        int depth = state.depth;
        // 访问 cur 节点，同时知道它所在的层数
        cout << "depth = " << depth << ", val = " << cur->val << endl;

        for (Node* child : cur->children) {
            q.push(State(child, depth + 1));
        }
    }
}


// 图结构的 BFS 遍历，从节点 s 开始进行 BFS，且记录路径的权重和
// 每个节点自行维护 State 类，记录从 s 走来的权重和
class State {
public:
    // 当前节点 ID
    int node;
    // 从起点 s 到当前节点的权重和
    int weight;

    State(int node, int weight) : node(node), weight(weight) {}
};

void bfs(const Graph& graph, int s) {
    vector<bool> visited(graph.size(), false);
    queue<State> q;

    q.push(State(s, 0));
    visited[s] = true;

    while (!q.empty()) {
        State state = q.front();
        q.pop();
        int cur = state.node;
        int weight = state.weight;
        cout << "visit " << cur << " with path weight " << weight << endl;
        for (const Edge& e : graph.neighbors(cur)) {
            if (!visited[e.to]) {
                q.push(State(e.to, weight + e.weight));
                visited[e.to] = true;
            }
        }
    }
}
```

对于加权图，由于每条边的权重不同，遍历的步数不再能代表最短路径的长度，所以需要每个节点用自定义  `State`  类维护自己的路径权重和，最典型的例子就是  Dijkstra 单源最短路径算法   了。
