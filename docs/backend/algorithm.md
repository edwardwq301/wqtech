---
comments: true
---


## sort
insert sort: In iteration i, swap a[i] with each larger entry to its left.

```cpp
void insertSort(vector<int> &todo) {
    for (int i = 0; i < todo.size(); ++i) {
        for (int j = i; j > 0; --j) {
            if (todo[j] < todo[j - 1])
                swap(todo[j], todo[j - 1]);
            else break;
        }
    }
}
```

我看国内挺多都是先一直在赋值，最后放正确位置

```cpp
void insertionSort(std::vector<int>& arr) {
    int n = arr.size();
    for (int i = 1; i < n; ++i) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j = j - 1;
        }
        arr[j + 1] = key;
    }
}
```

select sort: In iteration i, swap a[i] with proper item.

```cpp
void selectSort(vector<int> &todo) {
    for (int i = 0; i < todo.size(); i++) {
        int min = i;
        for (int j = min + 1; j < todo.size(); j++) {
            if (todo[j] < todo[min])
                min = j;
        }
        swap(todo[i], todo[min]);
    }
}
```

shell sort: in insertion sort, some item should move lots step while just move step by step. so we move some item by long distance and shrink the distance 

```cpp
void shellSort(vector<int> &todo) {
    int N = todo.size();
    int h = 1;
    while (h < N / 3) h = 3 * h + 1;

    while (h >= 1) {
        for (int i = h; i < N; i++) {
            for (int j = i; j >= h && todo[j] < todo[j - h]; j -= h)
                swap(todo[j], todo[j - h]);
        }
        h = h / 3;
    }
}
```

bubble sort: 每轮冒泡结束的有序部分是全局有序，插入排序（insert sort）是局部有序

```cpp
void BubbleSort (vector < int >&num){
    for (int i = 0; i < num.size (); i++){
        bool flag = false;
        for (int j = num.size () - 1; j > i; j--)
            if (num[j - 1] > num[j]){
                swap (num[j - 1], num[j]);
                flag = true;
            }
        if (flag == false)
            return;
        }
}
```

knuth shuffle: in iteration i, swap a[i] and a[k] (random k in [0, i])

非常奇怪，[课程视频链接](https://www.coursera.org/learn/algorithms-part1/lecture/12vcF?t=432)是第一种

```cpp
void shuffle(vector<int> &todo) {
    int N = todo.size();
    for (int i = 0; i < N; i++) {
        int k = rand() % (i + 1);   //[0, i]
        swap(todo[i], todo[k]);
    }
}
```

网上大多是第二种，[维基百科](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle)给的和第一种不一样，挺奇怪的，不知道哪个对

```cpp
void shuffle(vector<int>& todo){
    for(int i= todo.size()-1;i>0;i--){
        int k= rand() % (i+1)
        swap(todo[i], todo[k])
    }
}
```

```txt
wiki

-- To shuffle an array a of n elements (indices 0..n-1):
for i from n−1 down to 1 do
     j ← random integer such that 0 ≤ j ≤ i
     exchange a[j] and a[i]


-- To shuffle an array a of n elements (indices 0..n-1):
for i from 0 to n−2 do
     j ← random integer such that i ≤ j < n
     exchange a[i] and a[j]
```

## 环形队列
容量为 n 的环形队列，读写指针拓展为 `原始[0, n-1] 镜像[n, 2n-1]`。当指针值大于等于 2n 时，使其折返到 ptr-2n。

真正读写的索引：`in = in % n` 等价于 `in = in & (in-1)`

如何判断空满：

1. 两个符号，如果进入镜像就🚩，两个符号相同说明空，不同为满
2. 如果队列容量是2的幂次，可以不用上边两个符号位。改进做法为：
   1. 读写指针相同 空；相差 n 满;等价于 `out == (in xor size)`
   2. [Linux使用无符号数进阶](https://zh.wikipedia.org/wiki/%E7%92%B0%E5%BD%A2%E7%B7%A9%E8%A1%9D%E5%8D%80#Linux%E5%86%85%E6%A0%B8%E7%9A%84kfifo)

[参考资料](https://juejin.cn/post/7113550346835722276?searchId=202408141508525F245388AC3E2679B7EA#heading-0)

## 并查集

```java
public class Union {
    private int id[];
    private int size[];

    public Union(int n) {
        id = new int[n];
        size = new int[n];
        for (int i = 0; i < n; i++) {
            id[i] = i;
            size[i] = 1;
        }
    }

    private int root(int i) {
        while (i != id[i]) {
            //id[i] = id[id[i]]; 没有就是基础版
            i = id[i];
        }
        return i;
    }

    public void union(int p, int q) {
        int pro = root(p);
        int qro = root(q);
        if (size[pro] <= size[qro]) {
            id[pro] = qro;
            size[qro] += size[pro];
        }
        else {
            id[qro] = pro;
            size[pro] += size[qro];
        }
    }

    public boolean connected(int p, int q) {
        return root(q) == root(q);
    }
}

```

*Successor* with delete: Given a set of n integers S = {0, 1, ..., n−1} and a sequence of requests of the following form:

- remove x from S 
- find *successor* of  x : the smallest y in S such that y>=x
- solve hint: union(x,x+1)

- 假定不考虑删除或查询最后一个，如果把右边的挂到左边，就在 `actual` 记录一下
- [思路来源](https://stackoverflow.com/a/52564894)

??? "slove"

    ```java
    public class Union {
        public static void main(String[] args) {
            Union ua = new Union(7);
            ua.remove(2);
            ua.remove(4);
            ua.remove(3);
            System.out.println("successor 3: " + ua.successor(3));
            ua.remove(5);
            System.out.println("successor 5: " + ua.successor(5));
            ua.showActual();
        }

        private int id[];
        private int size[];
        private int actual[];

        public Union(int n) {
            id = new int[n];
            size = new int[n];
            actual = new int[n];
            for (int i = 0; i < n; i++) {
                id[i] = i;
                actual[i] = i;
                size[i] = 1;
            }
        }

        private int root(int i) {
            while (i != id[i]) {
                id[i] = id[id[i]];
                i = id[i];
            }
            return i;
        }

        public void union_ab(int p, int q) {
            /* p < q */
            int pro = root(p);
            int qro = root(q);
            if (size[pro] <= size[qro]) {
                id[pro] = qro;
                size[qro] += size[pro];
            }
            else {
                id[qro] = pro;
                size[pro] += size[qro];
                actual[pro] = qro;
            }
        }

        public boolean connected(int p, int q) {
            return root(q) == root(q);
        }

        public void remove(int x) {
            if (x >= 0 && x < id.length - 1)
                union_ab(x, x + 1);
        }

        public int successor(int x) {
            return actual[root(x + 1)];
        }

        public void showActual() {
            System.out.print("actual: ");
            for (int x = 0; x < actual.length - 1; x++)
                System.out.print(actual[root(x + 1)] + " ");

            System.out.print("\nroot: ");
            for (int x = 0; x < actual.length; x++)
                System.out.print(id[x] + " ");
        }
    }
    ```

## 堆
最大堆，（最小堆把符号变一下）

```cpp
class maxHeap {
    vector<int> tree;
    int N = 0;

    //change bottom to top
    void swim(int k) {
        while (k > 1 && tree[k] > tree[k / 2]) {
            swap(tree[k], tree[k / 2]);
            k = k / 2;
        }
    }
    //change top to down
    void sink(int k) {
        while (k * 2 <= N) {    //may have 4 node and k=2
            int j = k * 2;
            if (j < N && tree[j + 1] > tree[j]) j++;
            if (tree[k] >= tree[j]) break;
            swap(tree[k], tree[j]);
            k = j;
        }
    }

public:
    maxHeap(int n) {
        tree.resize(n + 1);
    }


    void add(int k) {
        tree[++N] = k;
        swim(N);
    }

    int delMax() {
        int max = tree[1];
        tree[1] = tree[N--];
        sink(1);
        return max;
    }
};
```

堆排序就相当于每次把最大元素放到结尾，个数减一，`sink(1)` 调整一下

## 树
中序遍历二叉树，非递归双色法。好处是只要逆序遍历顺序就行，例如：先序遍历，代码改成 `st.push(root.right,false), st.push(root.left,true), st.push(root,false)`。 [教程](https://leetcode.cn/problems/binary-tree-inorder-traversal/solutions/25220/yan-se-biao-ji-fa-yi-chong-tong-yong-qie-jian-ming)

??? "双色标记"

    ```cpp
    class Solution {
    public:

        vector<int> inorderTraversal(TreeNode *root) {
            vector<int> anw;

            if (root == nullptr) return anw;

            stack<pair<TreeNode *, bool>> st;
            st.push(make_pair(root, false));
            while (!st.empty()) {
                auto [node, color] = st.top();
                st.pop();
                if (node == nullptr) {
                    cout << "null" << endl;
                    continue;
                }
                if (color) {
                    anw.emplace_back(node->val);
                    cout << "anw " << node->val << endl;
                    continue;
                }
                else {
                    st.push(make_pair(node->right, false));
                    st.push(make_pair(node, true));
                    st.push(make_pair(node->left, false));
                    cout << "push right " << node->val << " push left" << endl;
                }
            }
            return anw;

        }
    };
    ```


Morris 遍历
用先往左子树走的模板说明前序遍历

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode *root) {
        vector<int> anw;
        if(root== nullptr) return anw;
        TreeNode*now=root;
        while (now){
            TreeNode* mirros=now->left;
            if(mirros== nullptr) { 
                // 可以看成访问到根节点/没有左子树的节点，不管哪种访问，根节点值一定要加
                anw.push_back(now->val);
                now=now->right;
                continue;
            }
            // 下边都是有子树的情况
            while (mirros->right&&mirros->right!=now)mirros=mirros->right;
            if(mirros->right== nullptr){ 
                // 第一次经过节点 A，前序在这里加入
                anw.push_back(now->val);
                mirros->right=now;
                now=now->left;
            }
            else{ // 第二次经过节点 A，中序在这里加入
                mirros->right= nullptr;
                now=now->right;
            }
        }
        return anw;
    }
};
```

[后序解释](https://leetcode.cn/problems/binary-tree-postorder-traversal/solutions/1132883/morrisbian-li-suan-fa-jie-xi-qian-xu-bia-dt2x)：获取根右左的顺序，最后反转，建立指针的时候和前中序遍历不一样


=== "前序"

    ```cpp
    class Solution {
    public:
        vector<int> preorderTraversal(TreeNode *root) {
            vector<int> anw;
            if (root == nullptr) return anw;
            TreeNode *now = root;
            while (now) {
                TreeNode *mirrs = now->left;
                if (mirrs == nullptr) {
                    anw.push_back(now->val);
                    now = now->right;
                    continue;
                }
                while (mirrs->right && mirrs->right != now) mirrs = mirrs->right;
                if (mirrs->right == nullptr) {
                    anw.push_back(now->val);
                    mirrs->right = now;
                    now = now->left;
                }
                else {
                    mirrs->right = nullptr;
                    now = now->right;
                }
            }
            return anw;
        }
    };
    ```

=== "中序"

    ```cpp
    class Solution {
    public:
        vector<int> inorderTraversal(TreeNode *root) {
            vector<int> anw;
            if (root == nullptr) return anw;
            TreeNode *now = root;
            while (now) {
                TreeNode *mirrs = now->left;
                if (mirrs == nullptr) {
                    anw.push_back(now->val);
                    now = now->right;
                    continue;
                }
                while (mirrs->right && mirrs->right != now) mirrs = mirrs->right;
                if (mirrs->right == nullptr) {
                    mirrs->right = now;
                    now = now->left;
                }
                else {
                    mirrs->right = nullptr;
                    anw.push_back(now->val);
                    now = now->right;
                }
            }
            return anw;
        }
    };
    ```

=== "后序"

    ```cpp
    class Solution {
    public:
        vector<int> postorderTraversal(TreeNode *root) {
            vector<int> res;
            if (root == nullptr) return res;
            TreeNode *now = root;
            while (now) {
                TreeNode *mirrs = now->right;
                if (mirrs == nullptr) {
                    res.push_back(now->val);
                    now = now->left;
                    continue;
                }
                while (mirrs->left && mirrs->left != now) mirrs = mirrs->left;
                if (mirrs->left == nullptr) {
                    res.push_back(now->val);
                    mirrs->left = now;
                    now = now->right;
                }
                else {
                    mirrs->left = nullptr;
                    now = now->left;
                }
            }
            std::reverse(res.begin(), res.end());
            return res;
        }
    };
    ```

## 图
[图的存储](https://blog.csdn.net/raelum/article/details/129108365)

### 无向图
- dfs 求路径
- dfs 进入顺序，完成顺序（后序），逆完成顺序
- 求连通分量
- 二分图

```cpp
#include "wq.h"

const int vertexCnt = 10;

class Graph {
public:
    int V;
    int E;
    vector<int> adj[vertexCnt];

    Graph(int vCnt, int eCnt) {
        this->V = vCnt;
        this->E = eCnt;
        for (int i = 0; i < eCnt; ++i) {
            int a, b;
            cin >> a >> b;
            addEdge(a, b);
        }
    }

    void addEdge(int a, int b) {
        adj[a].push_back(b);
        adj[b].push_back(a);
    }

};

class DepthFirstPaths {

    bool marked[vertexCnt];
    int edgeTo[vertexCnt];
    int start;
public:
    DepthFirstPaths(const Graph &g, int start) {
        this->start = start;
        dfs(g, start);
    }

    void dfs(const Graph &g, int v) {
        marked[v] = true;
        for (int u: g.adj[v]) {
            if (!marked[u]) {
                edgeTo[u] = v;
                dfs(g, u);
            }
        }
    }

    bool havePathTo(int v) {
        return marked[v];
    }

    vector<int> pathTo(int v) {
        vector<int> path;
        if (!havePathTo(v)) return path;
        for (int x = v; x != start; x = edgeTo[x])
            path.push_back(x);
        path.push_back(start);
        reverse(path.begin(), path.end());
        return path;
    }
};

class DepthFirstOrder {

    bool marked[vertexCnt];

public:
    vector<int> pre;
    vector<int> post;
    stack<int> reversePost_stack;
    vector<int> reverstpost_vec;

    explicit DepthFirstOrder(const Graph &g) {
        for (int v = 0; v < g.V; ++v)
            if (!marked[v])
                dfs(g, v);

        reverse(reverstpost_vec.begin(), reverstpost_vec.end());
    }


private:
    void dfs(const Graph &g, int s) {

        pre.push_back(s);

        marked[s] = true;
        for (int w: g.adj[s])
            if (!marked[w])
                dfs(g, w);

        post.push_back(s);
        reversePost_stack.push(s);
        reverstpost_vec.push_back(s);
    }
};


// 无向图连通分量
class CC {
    bool marked[vertexCnt];
    int id[vertexCnt];
    int count = 0;
public:
    explicit CC(const Graph &g) {
        // vertex begin from 0
        for (int s = 0; s < g.V; s++) {
            if (!marked[s]) {
                dfs(g, s);
                count++;
            }
        }
    }

    bool isconnected(int u, int v) {
        return id[v] == id[u];
    }

    int idof(int v) { return id[v]; }

    int countOfCC() const { return count; }

private:
    void dfs(const Graph &g, int v) {
        marked[v] = true;
        id[v] = count;
        for (int t: g.adj[v]) {
            if (!marked[t]) {
                dfs(g, t);
            }
        }
    }
};

class Cycle {
    bool marked[vertexCnt];
    bool haveCycle;

public:
    Cycle(const Graph &g) {
        for (int s = 0; s < g.V; ++s)
            if (!marked[s])
                dfs(g, s, s);
    }

    bool hasCycle() const { return haveCycle; }

private:
    void dfs(const Graph &g, int v, int u) {
        marked[v] = true;
        for (int w: g.adj[v]) {
            if (!marked[w])
                dfs(g, w, v);
            else if (w != u) haveCycle = true;
        }
    }

};

class TwoColor {
    bool marked[vertexCnt];
    bool color[vertexCnt];
    bool isTwoColorable = true;
public:
    bool isTwocolorable() const { return isTwoColorable; }

    explicit TwoColor(const Graph &g) {
        for (int s = 0; s < g.V; ++s)
            if (!marked[s])
                dfs(g, s);
    }

private:
    void dfs(const Graph &g, int v) {
        marked[v] = true;
        for (int u: g.adj[v]) {
            if (!marked[u]) {
                color[u] = !color[v];
                dfs(g, u);
            }
            else if (color[v] == color[u]) isTwoColorable = false;
        }
    }

};

void test() {

    Graph undirected(6, 6);
//    DepthFirstPaths path(undirected, 1);
//    vector<int> pathto4 = path.pathTo(4);
//    cout << "path to 4 is ";
//    for (int x: pathto4)
//        cout << x << ' ';
//    cout << endl;

//    CC cc(undirected);
//    cout << cc.countOfCC();

//    Cycle cycle(undirected);
//    cout << cycle.hasCycle();

//    TwoColor cantwocolor(undirected);
//    cout << cantwocolor.isTwocolorable();

    DepthFirstOrder order(undirected);
    cout << setw(30) << std::left << "dfs order: ";
    for (int x: order.pre) cout << x << ' ';
    cout << endl;

    cout << setw(30) << "dfs complete order: ";
    for (int x: order.post) cout << x << ' ';
    cout << endl;

    cout << setw(30) << "dfs complete reverse order:";
    for (int x: order.reverstpost_vec) cout << x << ' ';
    cout << endl;
    while (!order.reversePost_stack.empty()) {
        cout << order.reversePost_stack.top() << ' ';
        order.reversePost_stack.pop();
    }
    cout << endl;
}

int main() {
    test();
    return 0;
}
```


拓扑排序：dfs的结果逆序一下就是答案，或者每次找入度为零得点，用当前点更新别的点得入度
```cpp
bool marked[graph.v()];
vector<int> toReverse;

vector<int> topoSort() {
    topoSortCore(0);
    return reverse(toReverse.begin(), toReverse.end());
}

void topoSortCore(int vertex) {
    marked[vertex] = true;
    for (int v : vetex.adj())
        if (!marked[v])
            topoSortCore(v);
    toReverse.push(vertex);
}
```

求强连通分量：先对**逆图（边全反向）**求出拓扑排序的节点顺序，假如是 `3,2,1,4,6,7`，然后按着这个顺序对**原图** dfs 得强连通分量



### 有向图
有向图判断有没有环路

```cpp
class DirectedGraph {
public:
    int V;
    int E;
    vector<int> adj[N];

    DirectedGraph(int Vcnt) : V(Vcnt) {}

    DirectedGraph(int Vcnt, int Ecnt) : V(Vcnt), E(Ecnt) {}

    void addEdge(int from, int to) { adj[from].push_back(to); }

};

class DirectedCycle {
    bool marked[N] = {false};
    bool onStack[N] = {false};
    int edgeTo[N] = {0};

    bool hasCycle() { return !cycle.empty(); }

public:
    stack<int> cycle;

    DirectedCycle(const DirectedGraph &G) {
        for (int v = 0; v < G.V; ++v) {
            if (!marked[v]) dfs(G, v);
        }
    }

    void dfs(const DirectedGraph &G, int v) {
        onStack[v] = true;
        marked[v] = true;
        for (int w: G.adj[v])
            if (hasCycle()) return;
            else if (!marked[w]) {
                edgeTo[w] = v;
                dfs(G, w);
            }
            else if (onStack[w]) {
                for (int x = v; x != w; x = edgeTo[x])
                    cycle.push(x);

                cycle.push(w);
                cycle.push(v);
            }
        onStack[v] = false;
    }

    stack<int> getCycle() { return cycle; }
};
```

单源最短路 迪杰斯特拉

```cpp

struct OutEdge {
    int to, weight;

    bool operator<(OutEdge other) const { return this->weight > other.weight; }

};

class WeightDirectedGraph {
    int E;
public:

    int V;

    WeightDirectedGraph(int Vcnt) : V(Vcnt) {}

    WeightDirectedGraph(int Vcnt, int Ecnt) : V(Vcnt), E(Ecnt) {}

    vector<OutEdge> adjs[N];

    void addEdge(int from, int to, int weight) {
        adjs[from].push_back({to, weight});
    }
};


class DijkSP {
    vector<int> dis;
    vector<int> pathTo;
public:
    DijkSP(const WeightDirectedGraph &G, int src) {
        pathTo = vector<int>(G.V);
        dis = vector<int>(N, 0x3f3f3f3f);
        priority_queue<OutEdge> pq; // minheap
        vector<bool> marked(G.V, false);
        dis[src] = 0;
        pq.push({0, 0});

        while (!pq.empty()) {
            int temV = pq.top().to;
            pq.pop();
            if (marked[temV]) continue;
            marked[temV] = true;
            for (auto adj: G.adjs[temV]) {
                int u = adj.to;
                int disVtoU = adj.weight;
                if (dis[temV] + disVtoU < dis[u]) {
                    dis[u] = dis[temV] + disVtoU;
                    pathTo[u] = temV;
                }
                pq.push({u, dis[u]});
            }
        }
    }

    vector<int> getDis() { return dis; }

    vector<int> getPath() { return pathTo; }
};


void test() {
    WeightDirectedGraph g(3, 3);
    g.addEdge(0, 1, 1);
    g.addEdge(1, 2, 1);
    g.addEdge(0, 2, 10);
    DijkSP sp(g, 0);
    auto dis = sp.getDis();
    for (int x: dis) cout << x << ' ';
    cout << endl;
    dis = sp.getPath();
    for (int x: dis) cout << x << ' ';
}

int main() {
    test();
    return 0;
}
```

### 最小生成树
克鲁斯卡尔

```cpp
class Edge {
    int a, b;
public:
    int w;

    Edge(int u, int v, int weight) : a(u), b(v), w(weight) {}

    int other(int x) { return x == a ? b : a; }

    int either() { return a; }

    bool operator<(Edge other) const {
        return this->w > other.w;
    }
};

class EdgeWeightGraph {
public:
    vector<Edge> list;
    int E, V;

    EdgeWeightGraph(int Vcnt, int Ecnt) : V(Vcnt), E(Ecnt) {}

    void addEdge(int a, int b, int w) {
        list.emplace_back(a, b, w);
    }
};

class UnionFind {
    int root[N];
    int sz[N];

public:
    UnionFind() { for (int i = 0; i < N; ++i)root[i] = i, sz[i] = 1; }

    void unionTwo(int a, int b) {
        int ra = getRoot(a), rb = getRoot(b);
        if (sz[ra] < sz[rb]) {
            root[ra] = rb;
            sz[rb] += sz[ra];
        }
        else {
            root[rb] = ra;
            sz[ra] += sz[rb];
        }
    }

    bool connected(int a, int b) {
        return getRoot(a) == getRoot(b);
    }

    int getRoot(int a) {
        while (a != root[a]) {
            root[a] = root[root[a]];
            a = root[a];
        }
        return a;
    }
};

class KruskalMST {
    queue<Edge> mst;
    priority_queue<Edge, vector<Edge>> pq;
    UnionFind uf;
public:
    KruskalMST(const EdgeWeightGraph &G) {
        for (auto x: G.list)pq.push(x);

        while (!pq.empty() && mst.size() < G.V - 1) {
            Edge e = pq.top();
            pq.pop();
            int v = e.either(), w = e.other(v);
            if (uf.connected(v, w)) continue;
            uf.unionTwo(v, w);
            mst.push(e);
        }
    }

    queue<Edge> getMST() { return mst; }
};
```


## string 
### trie
力扣 208

```cpp
class Trie {
    int isEnd;
    Trie *next[26];
public:
    Trie() {
        isEnd = false;
        for (auto &i: next)
            i = nullptr;
    }

    void insert(string word) {
        Trie *cur = this;
        for (char x: word) {
            if (cur->next[x - 'a'] == nullptr) cur->next[x - 'a'] = new Trie();
            cur = cur->next[x - 'a'];
        }
        cur->isEnd = true;
    }

    bool search(string word) {
        Trie *cur = this;
        for (char x: word) {
            int id = x - 'a';
            if (cur->next[id]) cur = cur->next[id];
            else return false;
        }
        return cur->isEnd;
    }

    bool startsWith(string prefix) {
        Trie *cur = this;
        for (char x: prefix) {
            int id = x - 'a';
            if (cur->next[id]) cur = cur->next[id];
            else return false;
        }
        return true;
    }
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 */
```

### 马拉车
[讲解](https://www.zhihu.com/question/37289584/answer/465656849)

```cpp
class Solution {
public:
    string preprocess(string s) {
        string anw = "^";
        for (char x: s) {
            anw += '#';
            anw += x;
        }           //anw+='#'+x is wrong !
        anw += "#$";
        cout << anw << endl;
        return anw;
    }

    string longestPalindrome(string s) {
        if (s.empty()) return "";
        string newstr = preprocess(s);

        int center = 0, rightThresold = 0;
        vector<int> len(newstr.size(), 1);

        for (int i = 1; i < newstr.size(); ++i) { // while 中的 newstr[i-len[i]] i-len[i]>=0
            int i_mirror = 2 * center - i;
            if (i < rightThresold)
                len[i] = min(len[i_mirror], rightThresold - i);

            while (newstr[i + len[i]] == newstr[i - len[i]])
                len[i]++;

            if (i + len[i] > rightThresold) {
                rightThresold = i + len[i];
                center = i;
            }
        }

        int begin_index = 0, maxlen = 0;
        for (int i = 0; i < len.size(); ++i) {
            if (len[i] > maxlen) {
                maxlen = len[i];
                begin_index = i;
            }
        }
        cout << begin_index << maxlen;
        return s.substr((begin_index - maxlen) / 2, maxlen - 1);
    }
};
```

### KMP
出自编译原理那本龙书（汉化第二版 86 页）

下面都是**从 1 开始计数**

1. 先构造函数 `f(s)` 表示 $$ b_1b_2..b_{f(s)} $$ 既是整个 pattern 的 s 位**真**前缀，也是 s 位后缀，两者相同。
2. 当匹配 s 个字母成功，s+1 个字母失败时，将 pattern 移动 s-f(s) 位 

```txt
no          1 2 3 4 5 6
word        a b a b x .... // fail on no.5, s = 4 and move 4-2=2 char
pattern     a b a b a b
f(s)        0 0 1 2 3 4

---
word        a b a b x ....
pattern         a b a b a b

```

在实现的时候从 1 开始和从 0 开始有不同，

如何计算 lose：字符不等就试一下上一个长度，正好 id 等于上一个长度能比较字符。

eg: `ababax` -> 比较 `ababax`  发现 x != b 下一个比 `aba` `abx`

下面是对 `s-f(s)` 进行修改

1. 在扫描的时候比较当前位，不是下一位
2. 假如在从 1 数第 5 位失败 s = 4, pat 整个前移 `s - f(s)= 4 - f(4) = 2` 位
3. 转换成下标就是从 0 数，下标 id 为 4 失败，pat 整个前移 `id - f(id - 1) = 4 - 2`
4. 字符串整个前移等价于扫描点回移，所以新的扫描点是 `id - (id - f(id-1) ) = f(id - 1)`

|计数起点|成功|失败|转换|
|:--:|:--|:--|:--|
|1|s 为 4 成功|s 为 5 失败|s - f(s) = 4 - f(4)|
|0|id 为 3 成功| id 为 4 失败| id - f(id-1) |

```cpp
#include "vector"
#include "string"
#include"iostream"

using namespace std;

vector<int> SolveLose(string pattern) {
    // lose 可以看成 以下标为结尾的 符合条件的长度
    vector<int> lose(pattern.size(), 0);

    for (int slow = 0, fast = 1; fast < pattern.size(); fast++) {
       
        while (slow > 0 && pattern[slow] != pattern[fast]) slow = lose[slow - 1];
        if (pattern[slow] == pattern[fast]) {
            slow++;
            lose[fast] = slow;
        }
        else lose[fast] = 0;
    }

    // for (int x: lose)cout << x << ' ';
    return lose;
}

void Search(string word, string pat) {
    auto lose = SolveLose(pat);
    int w = 0, p = 0;
    while (w < word.size()) {
        if (word[w] == pat[p]) {
            w++;
            p++;
        }
        else if (p > 0) p = lose[p - 1];
        else w++;

        if (p == pat.size()) {
            cout << w - pat.size() << ' ';
            p = lose[p - 1];
        }
    }

}

int main() {
    int n, m;
    string pat, word;
    cin >> pat >> word;
    Search(word, pat);
    return 0;
}
```

#### 红皮书
待续

```cpp
#include <algorithm>
#include <bits/stdc++.h>
#include <string>

using namespace std;
const int txtSize = 30;
const int typeCnt = 26;

int dfa[typeCnt][txtSize];

void kmp(string pat) {
    int patSize = pat.size();
    dfa[pat[0] - 'a'][0] = 1;
    for (int X = 0, j = 1; j < patSize; j++) {
        for (int i = 0; i < typeCnt; i++)
            dfa[i][j] = dfa[i][X];
        dfa[pat[j] - 'a'][j] = j + 1;
        X = dfa[pat[j] - 'a'][X];
    }
}

int search(string txt, string pat) {
    kmp(pat);
    int i, j;
    for (j = 0, i = 0; i < txt.size() && j < pat.size(); i++)
        j = dfa[txt[i] - 'a'][j];
    if (j == pat.size())
        return i - j;
    else
        return -1;
}



void test() {
    string pat = "baaa";
    string txt = "ababaaabac";
    kmp(pat);
    cout << search(txt, pat);
}

int main() {
    test();
    return 0;
}
```


## 数学
### 求最大公因数
辗转相除法，假如 `a=bx+y (a>b)`, k 是 a b 的公因子，y=a-bx=a%b，所以 k 也是 y 的因子，所以 k 是 a, b, a%b 的公因子，因为 k 任意，所以 a,b 的最大公因子也相等，可以用 a%b 缩小。另外假如 `a < b`，函数会调成 `gcd(b,a)`，又回到大数在第一个参数

```cpp
int gcd(int a, int b) {
    if (b == 0) return a;
    else return gcd(b, a % b);
}
```

### 快速幂
```cpp

typedef long long LL;

LL quick_power(int base, int power) {
    LL anw = 1;
    while (power > 0) {
        if (power & 1)
            anw *= base;
        base *= base;
        power >>= 1;
    }
    return anw;
}
LL quick_power_recursive(int base, int power) {
    if (power == 0)
        return 1;
    else {
        if (power & 1)
            return base * quick_power_re(base, power - 1);
        else {
            LL anw = quick_power_re(base, power / 2);
            return anw * anw;
        }
    }
}
```

### 质数筛
[埃氏筛](https://swtch.com/~rsc/thread/#:~:text=As%20another%20example%2C%20which%20Hoare%20credits%20to%20Doug%20McIlroy%2C%20consider%20the%20generation%20of%20all%20primes%20less%20than%20a%20thousand.%20The%20sieve%20of%20Eratosthenes%20can%20be%20simulated%20by%20a%20pipeline%20of%20processes%20executing%20the%20following%20pseudocode%3A)

??? "例子"

    ```cpp
    #include <bits/stdc++.h>

    using namespace std;
    //埃氏筛未优化
    vector<int> get_prime_core(vector<int>& anw, vector<int> source) {
        if (!source.empty())
            anw.push_back(source[0]);
        else
            return anw;
        vector<int> todo;
        for (int x : source) {
            if (x % source[0] != 0) {
                todo.push_back(x);
            }
        }
        return get_prime_core(anw, todo);
    }

    vector<int> get_prime(int n) {
        vector<int> anw;
        vector<int> source(n - 1);
        for (int i = 0, begin = 2; i < source.size(); i++, begin++)
            source[i] = begin;
        return get_prime_core(anw, source);
    }


    //埃氏筛优化
    vector<int> get_prime_update_ei(int n) {
        vector<bool> is_prime(n + 1, true);
        vector<int> anw;

        for (int i = 2; i <= n; i++) {
            if (is_prime[i])
                anw.push_back(i);
            else
                continue;
            for (int id = i*2; id <= n; id += i)
                is_prime[id] = false;
        }
        for (auto x : anw)
            cout << x << ' ';
        cout << endl
            << anw.size();
        return anw;
    }


    //线性筛
    vector<int> get_prime_update_On(int n) {
        vector<bool> is_prime(n + 1, true);
        vector<int> anw;

        for (int i = 2; i <= n; i++) {
            if (is_prime[i])
                anw.push_back(i);
            for (int j = 0; anw[j] <= n / i; j++) {
                is_prime[anw[j] * i] = false;
                if (i % anw[j] == 0)
                    break;
            }
        }

        for (auto x : anw)
            cout << x << ' ';
        cout << endl
            << anw.size();
        return anw;
    }

    int main() {
        int n = 20;
        vector<int> anw = get_prime_update_On(n);

        return 0;
    }
    ```

### 进制转换
除以基数取余（先得到的是低位）
```cpp
void change_base(int n, const int base) {
    string anw;
    do {
        anw = to_string(n % base) + anw;
        n /= base;
    } while (n > 0);
    cout << anw;
}
```

## 模板
### 分组判断
双指针的一种，也还行，双指针有时候写不好还要判断最后一段，例题为力扣 1146

[例题合集](https://leetcode.cn/problems/summary-ranges/solutions/553645/hui-zong-qu-jian-by-leetcode-solution-6zrs/comments/2106748)

=== "双指针"

    ```cpp
    class Solution {
    public:
        int maxPower(string s) {
            int anw = 0;
            for (int left = 0, right = 0; right < s.size(); right++) {
                if (right < s.size() - 1 && s[right] == s[right + 1]) continue;
                anw = max(anw, right - left + 1);
                left = right + 1;
            }
            return anw;
        }
    };
    ```

=== "分组判断"

    ```cpp
    class Solution {
    public:
        int maxPower(string s) {
            int anw = 0;
            int begin = 0;
            while (begin < s.size()) {
                int tem = begin;
                while (s[begin] == s[begin + 1]) begin++;
                anw = max(anw, begin - tem + 1);
                begin++;
            }
            return anw;
        }
    };
    ```

其实这个也能改造 `for` ，但是感觉 `while` 思路更流畅，例子：力扣 2110

```cpp
int i = 0;
while (i < prices.size()) {
    int tem = i;
    while (i + 1 < prices.size() && prices[i + 1] + 1 == prices[i]) i++;
    int len = i - tem + 1;
    anw += (1ll + len) * len / 2;
    i++;
}
```

```cpp
for (int i = 0; i < prices.size(); i++) {
    int tem = i;
    while (i + 1 < prices.size() && prices[i + 1] + 1 == prices[i]) i++;
    int len = i - tem + 1;
    anw += (1ll + len) * len / 2;
}
```
### 归并排序

=== "normal"

    ```cpp linenums="1" hl_lines="19 20 22 32 33"
    #include "vector"
    #include "iostream"
    #include "algorithm"


    using namespace std;

    void check(vector<int> checkitem) {
        for (int i : checkitem) {
            cout << i << ' ';
        }
        cout << endl;
    }

    void mergeCore(vector<int> &a, vector<int> &aux, int lo, int hi) {
        if (lo >= hi) return;
        int mid = (lo + hi) / 2;

        mergeCore(a, aux, lo, mid);
        mergeCore(a, aux, mid + 1, hi);

        int i = lo, j = mid + 1, k = lo;

        while (i <= mid && j <= hi) {
            if (a[i] < a[j])
                aux[k++] = a[i++];
            else aux[k++] = a[j++];
        }
        while (i <= mid) aux[k++] = a[i++];
        while (j <= hi) aux[k++] = a[j++];

        for (int ti = lo, tk = lo; ti <= hi; ti++, tk++)
            a[ti] = aux[tk];


    }

    void mergeSort(vector<int> &a, int lo, int hi) {
        auto aux = a;
        mergeCore(a, aux, lo, hi);

    }

    void testSort() {

        vector<int> pre = {20, 19, 18, 17, 16};


        vector<int> anw = pre;
        std::sort(anw.begin(), anw.end());

        mergeSort(pre, 0, pre.size() - 1);

        check(pre);

    }

    int main() {
        testSort();
        return 0;
    }
    ```

=== "improve"

    ```cpp linenums="1" hl_lines="19 20 41"
    #include "vector"
    #include "iostream"
    #include "algorithm"


    using namespace std;

    void check(vector<int> checkitem) {
        for (int i: checkitem) {
            cout << i << ' ';
        }
        cout << endl;
    }

    void mergeCore(vector<int> &a, vector<int> &aux, int lo, int hi) {
        if (lo >= hi) return;
        int mid = (lo + hi) / 2;

        mergeCore(aux, a, lo, mid);
        mergeCore(aux, a, mid + 1, hi);

        int i = lo, j = mid + 1, k = lo;

        while (i <= mid && j <= hi) {
            if (a[i] < a[j])
                aux[k++] = a[i++];
            else aux[k++] = a[j++];
        }
        while (i <= mid) aux[k++] = a[i++];
        while (j <= hi) aux[k++] = a[j++];

    //    for (int ti = lo, tk = lo; ti <= hi; ti++, tk++)
    //        a[ti] = aux[tk];


    }

    void mergeSort(vector<int> &a, int lo, int hi) {
        auto aux = a;
        mergeCore(a, aux, lo, hi);
        a = aux;
    }

    void testSort() {

        vector<int> pre = {20, 19, 18, 17, 16};


        vector<int> anw = pre;
        std::sort(anw.begin(), anw.end());

        mergeSort(pre, 0, pre.size() - 1);

        check(pre);

    }

    int main() {
        testSort();
        return 0;
    }
    ```


## DP
### 背包
经过空间优化后成一维的背包问题，直接写转移方程加判断条件，但是脑中想的是二维情况，写的是一维化简

01 背包求最大值 

$$ f[i][w]=max(f[i-1][w],value[i-1]+f[i][w-weight[i-1]]) $$

```cpp
for (int i = 1; i <= stones.size(); i++) {
    for (int w = target; w >= 0; w--) {
        if (w >= stones[i - 1])
            f[w] = max(f[w],
                       stones[i - 1] + f[w - stones[i - 1]]);
    }
}
```

为什么去掉物品维度后体积从不同顺序枚举

- 01背包计算本层 `f[x][y]` 用到上一层的前边 `f[x-1][a] a<y` 和上一层的上位 `f[x-1][y]`，为了避免上一层的值被本层的值覆盖了，所以从后往前枚举体积；画一下就明白了
- 完全背包公式推导后 `f[x][y]` 用的是本层的前边 `f[x][a] a<y` 和上一层的上位 `f[x-1][y]`，所以从大到小枚举体积

### 区间

Q: 为什么区间dp先枚举长度再枚举左端点

A: 防止用到还没算好的小区间 

eg:[最长回文字串"aaaaa"](https://leetcode.cn/problems/longest-palindromic-substring/description/)

用 `dp[0][4]` 的时候应该先算 `dp[1][3]` ,但是先枚举左端点的话就没做到先算 `dp[1][3]`

??? "wrong answer"

    ```C++
    class Solution {
    public:
        string longestPalindrome(string s) {

            vector<vector<bool>> dp(s.size(), vector(s.size(), false));
            pair<int, int> anw = {0, 1};
            for (int i = 0; i < s.size() - 1; ++i) {
                dp[i][i] = true;
                if (s[i] == s[i + 1]) {
                    dp[i][i + 1] = true;
                    anw = {i, 2};
                }
            }
            dp[s.size() - 1][s.size() - 1] = true;


            for (int l = 0; l < s.size(); ++l) {
                for (int len = 2; len <= s.size(); ++len) {
                    if(len+l-1>=s.size()) continue;
                    int r = l + len - 1;

                    if (dp[l + 1][r - 1] == true && s[l] == s[r]) {
                        dp[l][r] = true;
                        cout<<l<<' '<<r<<endl;
                        if (len > anw.second)
                            anw = {l, len};
                    }
                }
            }

            return s.substr(anw.first, anw.second);

        }
    };
    ```

??? "correct answer"

    ```C++
    class Solution {
    public:
        string longestPalindrome(string s) {
            if (s.size() == 0 || s.size() == 1) return s;
            vector<vector<bool>> dp(s.size(), vector<bool>(s.size()));
            int maxlen = 1, begin = 0;
            for (int i = 0; i < s.size(); i++) {
                dp[i][i] = true;
                if (i < s.size() - 1 && s[i] == s[i + 1]) {
                    dp[i][i + 1] = true;
                    begin = i;
                    maxlen = 2;
                }
            }

            for (int len = 2; len <= s.size(); len++)
                for (int i = 0; i + len - 1 < s.size(); i++) {
                    int r = i + len - 1;
                    if (s[i] == s[r] && dp[i + 1][r - 1] == true) {
                        dp[i][r] = true;
                        maxlen = len;
                        begin = i;
                    }
                }

            return s.substr(begin, maxlen);
        }
    };
    ```


### 记忆化搜索
看[博主宫水三叶的刷题笔记](https://mp.weixin.qq.com/s?__biz=MzU4NDE3MTEyMA==&mid=2247485297&idx=1&sn=5ee4ce31c42d368af0653f60aa263c82&chksm=fd9cac6ecaeb25787e6da90423c5467e1679da0a8aaf1a3445475199a8f148d8629e851fea57&scene=178&cur_album_id=1748759632187047943#rd)的时候看到：我们可以先想出 dfs 怎么做，然后看dfs用到了几个可变维度，就是 dp 的维度，转成 dp

---

[oi介绍](https://oi-wiki.org/dp/memo/#%E5%BC%95%E5%85%A5)

[最长上升子序列](https://www.acwing.com/problem/content/description/897/)

在原始dfs中，会出现很多重复没有用的计算。

求数字8的位置时，把之前的3，1，2等就重复算了。

```
7
3 1 2 1 8 5 6
```

??? "原始dfs"
    ```cpp
    #include "bits/stdc++.h"

    using namespace std;

    const int N = 1010;
    int res = 0;
    int a[N];
    int dp[N];
    int n;

    int solve(int po) {
        dp[po] = 1;
        for (int i = 0; i < po; ++i) {
            if (a[i] < a[po]) dp[po] = max(dp[po], solve(i) + 1);
        }
        return dp[po];
    }

    int main() {
        cin >> n;
        for (int i = 0; i < n; ++i) {
            cin >> a[i];
        }
        for (int i = 0; i < n; ++i) {
            res = max(res, solve(i));
        }
        cout << res;
        return 0;
    }
    ```

那么我们就存一个记忆化数组，如果之前算过了直接返回算好的值，否则，继续算

??? "记忆化"
    ```cpp
    #include "bits/stdc++.h"

    using namespace std;

    const int N = 1010;
    int res = 0;
    int a[N];
    int dp[N];
    int n;

    int solve(int po) {
        // 算好了就返回
        if (dp[po] != -1) return dp[po];

        dp[po] = 1;
        for (int i = 0; i < po; ++i) {
            if (a[i] < a[po]) dp[po] = max(dp[po], solve(i) + 1);
        }
        return dp[po];
    }

    int main() {

        ::memset(dp, -1, sizeof dp);


        cin >> n;
        for (int i = 0; i < n; ++i) {
            cin >> a[i];
        }
        for (int i = 0; i < n; ++i) {
            res = max(res, solve(i));
        }
        cout << res;
        return 0;
    }
    ```



