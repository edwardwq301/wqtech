---
comments: true
---

## data structure

### sort
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

shell sort: in select sort, some item should move lots step while just move step by step. so we move some item by long distance and shrink the distance 

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

knuth shuffle: in iteration i, swap a[i] and a[k] (random k in [0, i])

```cpp
void shuffle(vector<int> &todo) {
    int N = todo.size();
    for (int i = 0; i < N; i++) {
        int k = rand() % (i + 1);   //[0, i]
        swap(todo[i], todo[k]);
    }
}
```


### 并查集

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

### 堆
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

## template

vector删除特定元素 `nums.erase(remove(nums.begin(), nums.end(), val) ,nums.end());`

mergesort

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

heap
??? "stl"
    ```cpp
    #include "bits/stdc++.h"

    using namespace std;

    vector<int> heap;

    int main() {
        int n;
        cin >> n;
        while (n--) {
            int op;
            cin >> op;
            if (op == 1) {
                int x;
                cin >> x;
                heap.push_back(x);
                push_heap(heap.begin(), heap.end(), greater<int>());
    //            make_heap(heap.begin(), heap.end(), greater<int>());
            }
            else if (op == 2)
                cout << heap.front() << endl;
            else {
                pop_heap(heap.begin(), heap.end(), greater<int>());
                heap.pop_back();
            }

        }
        return 0;
    }
    ```

## thought

[图的存储](https://blog.csdn.net/raelum/article/details/129108365)

### Q: 为什么区间dp先枚举长度再枚举左端点

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



### manacher

[讲解](https://www.zhihu.com/question/37289584/answer/465656849)

??? "slove"

    ```
    class Solution {
    public:
        string longestPalindrome(string s) {
            string todo = "";

            for (int i = 0; i < s.size(); i++)
                todo += s[i], todo += '#';
            todo = "^#" + todo;
            todo += '$';

            vector<int> p(todo.size(), 1);
            int id = 0, maxid = 0;
            for (int i = 1; i < todo.size(); i++) {
                if (i < maxid) {
                    p[i] = min(p[2 * id - i], maxid - i);
                }

                while (todo[i - p[i]] == todo[i + p[i]])
                    p[i]++;
                if (p[i] + i > maxid) {
                    maxid = i + p[i];
                    id = i;
                }
            }


            int st = 0, len = 0;
            for (int i = 1; i < p.size() - 1; i++) {
                if (p[i] > len) {
                    st = i;
                    len = p[i];
                }
            }
            cout << st << ' ' << len;
            return s.substr((st - len) / 2, len - 1);
        }
    };
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
            for (int id = i; id <= n; id += i)
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




