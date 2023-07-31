### [lakes](https://codeforces.com/contest/1829/problem/E)
??? lakes
    这个题在于剪枝，有的不用再dfs了，不然超时。假如（1，1）和（1，2）联通，dfs（1，1）和dfs（1，2）是一个结果。
    ```C++
    #include <cstring>
    #include "iostream"

    using namespace std;
    const int N = 1010;
    int gra[N][N];
    int n, m;
    int total;
    int dx[4] = {0, 0, -1, 1};
    int dy[4] = {1, -1, 0, 0};
    bool visited[N][N];


    int dfs(int a, int b) {
        visited[a][b] = true;
        if (gra[a][b] == 0) return 0;
        int anw = gra[a][b];

        for (int i = 0; i < 4; i++) {
            int nx = a + dx[i];
            int ny = b + dy[i];
            if (nx >= 1 && nx <= n && ny >= 1 && ny <= m
                && gra[nx][ny] > 0 && visited[nx][ny] == false) { anw += dfs(nx, ny); }
        }
        return anw;
    }

    void solve() {

        int fin = 0;
        cin >> n >> m;

        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= m; j++)
                cin >> gra[i][j];


        memset(visited, 0, sizeof visited);
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= m; j++) {
                if (gra[i][j] != 0 && visited[i][j] == false) {

                    fin = max(dfs(i, j), fin);
                }
            }
        cout << fin << endl;

    }

    int main() {

        cin >> total;
        while (total--)
            solve();
        return 0;
    }

    ```

### [Hits Different](https://codeforces.com/contest/1829/problem/G)
[前缀和动画讲解](https://usaco.guide/silver/more-prefix-sums?lang=cpp#2d-prefix-sums)
??? solve
    非常巧妙啊，转成前缀和,详情可以见相应英文题解
    ```C++
    #include "iostream"

    using namespace std;

    typedef long long  llint;
    llint anw[2050000];
    llint gra[2029][2029];
    llint cur = 1;

    void solve() {
        llint x;
        cin >> x;
        cout << anw[x] << endl;
    }

    int main() {
        ios::sync_with_stdio(false);
        cin.tie(nullptr);
        int n;
        for (int i = 1; i <= 2023; i++)
            for (int j = i; j >= 1; j--) {
                gra[j][i - j + 1] = gra[j - 1][i - j + 1] + gra[j][i - j + 1 - 1]
                                    - gra[j - 1][i - j + 1 - 1]
                                    + cur * cur;
                anw[cur] = gra[j][i - j + 1];
                cur++;
            }
        cin >> n;
        while (n--)
            solve();
        return 0;
    }
    ```

### [Distinct Split](https://codeforces.com/contest/1791/problem/D)
??? slove
    1. 一次遍历统计出所有字母的出现次数
    2. 从前往后开始算，给pre分一个字母，就在该字母出现总数-1
    3. 统计所有字母，进行加和；
    巧妙在相当于并行处理2个字符串，想不出来😥 


## template
### mergesort
??? "&"
    ```C++
    #include "iostream"
    #include "vector"

    using namespace std;

    void merge(vector<int> &nums, vector<int> &copy, int l, int r) {
        if (l == r) {
            copy[l] = nums[l];
            return;
        }
        int mid = l + r >> 1;
        merge(copy, nums, l, mid);
        merge(copy, nums, mid + 1, r);

        int i = l, j = mid + 1, idx = l;
        while (i <= mid && j <= r) {
            if (nums[i] < nums[j]) copy[idx++] = nums[i++];
            else copy[idx++] = nums[j++];
        }
        while (i <= mid)
            copy[idx++] = nums[i++];
        while (j<=r)
            copy[idx++] = nums[j++];

    }

    void mergesort(vector<int> &nums) {
        if (nums.size() <= 1) return;
        vector<int> copy = nums;
        merge(nums, copy, 0, nums.size() - 1);
        nums=copy;
    }

    int main() {
        vector<int> nums;
        int n;
        cin >> n;
        nums.resize(n);
        for (int i = 0; i < n; ++i) {
            cin >> nums[i];
        }
        mergesort(nums);
        for (auto x: nums)
            cout << x << ' ';
        return 0;
    }
    ```

