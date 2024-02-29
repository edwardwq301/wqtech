---
comment: true
---

## leetcode

### 倒水
有一个容量为8, 5, 3的桶，其中8为满，5和3为空，只能倒满和倒空，想一个办法得到4的水

bfs，注意搜过的状态不在搜，但是为什么这个类只能存到上一步不能存到上上步，很奇怪，求指点🥰

??? "slove"

    ```cpp
    #include <iostream>
    #include <queue>
    #include <set>
    #include <vector>
    using namespace std;

    class state {
        int bu8;
        int bu5;
        int bu3;
        state * father;

    public:
        state(int bu8, int bu5, int bu3) : bu8(bu8), bu5(bu5), bu3(bu3) {}
        vector<state> getPossState() {
            vector<state> poss;
            if (bu8 > 0) {
                if (bu5 < 5) {
                    int loss = min(5 - bu5, bu8);
                    poss.push_back(state(bu8 - loss, bu5 + loss, bu3));
                }
                if (bu3 < 3) {
                    int loss = min(3 - bu3, bu8);
                    poss.push_back(state(bu8 - loss, bu5, bu3 + loss));
                }
            }
            if (bu5 > 0) {
                if (bu8 < 8) {
                    int loss = min(8 - bu8, bu5);
                    poss.push_back(state(bu8 + loss, bu5 - loss, bu3));
                }
                if (bu3 < 3) {
                    int loss = min(3 - bu3, bu5);
                    poss.push_back(state(bu8, bu5 - loss, bu3 + loss));
                }
            }
            if (bu3 > 0) {
                if (bu8 < 8) {
                    int loss = min(8 - bu8, bu3);
                    poss.push_back(state(bu8 + loss, bu5, bu3 - loss));
                }
                if (bu5 < 5) {
                    int loss = min(5 - bu5, bu3);
                    poss.push_back(state(bu8, bu5 + loss, bu3 - loss));
                }
            }
            return poss;
        }

        bool isAnw() {
            return bu8 == 4 || bu3 == 4 || bu5 == 4;
        }

        int toInt() {
            int anw = bu8;
            anw *= 10;
            anw += bu5;
            anw *= 10;
            anw += bu3;
            return anw;
        }

        void setFather(state * fa) {
            father = fa;
        }

        state * getFather() {
            return father;
        }
    };



    void test() {
        int fat[900]; // fat store its father

        state begin(8, 0, 0);
        begin.setFather(nullptr);
        fat[800] = 0;

        queue<state> queue;
        queue.push(begin);
        set<int> visited;
        visited.insert(begin.toInt());

        while (!queue.empty()) {
            auto top = queue.front();
            queue.pop();

            for (state x : top.getPossState()) {
                if (visited.count(x.toInt()) == 0) {
                    visited.insert(x.toInt());
                    queue.push(x);
                    x.setFather(&top);

                    fat[x.toInt()] = top.toInt();
                    cout << x.toInt() << " father is " << x.getFather()->toInt()
                        << " father'address is " << x.getFather() << endl;
                }
                if (x.isAnw()) {
                    cout << "yes\n";
                    int k = x.toInt();
                    while (fat[k] != 0) {
                        cout << k << ' ';
                        k = fat[k];
                    }
                    cout << endl;
                    // why this is wrong, only can store its father, cannot store
                    // its grandfather?

                    while (x.toInt() != 800) {
                        cout << x.toInt() << ' ';
                        x = *(x.getFather());
                    }
                }
            }
        }
    }


    int main() {
        cout << endl;
        test();

        return 0;
    }
    ```

### 3 无重复字符的最长子串
要求的是最长字串（就是要挨着的字符），不是子序列

**小技巧**：把字符串前边加一个空格或者别的占位，这样计数的时候 `dp[i]` 就自然的表示以第 i 个字母结尾的性质，开空间用新的长度也可以正常访问

解法一：2024/1/19 想的是 dp，以 `dp[i]` 表示第 i 个字母结尾（闭区间）的最长子串

- 如果 `str[i]` 没出现过， `dp[i]=dp[i-1]+1`
- 如果出现过了，就是 `dp[i]=i-上一次出现的位置`，然后提交发现错了 `abba`，再分析一下，
    - 如果上一次 a 出现右边有重复 `eg: bb`，`dp[i]=dp[i-1]+1`
    - 如果没有重复，`dp[i]=i-上一次出现位置`。总之得出应该是两者取小
- 更新出现当前字母出现位置
```
a |qbbc|a

bb|...a....|a
```

??? "dp"

    ```cpp
    class Solution {
    public:
        int lengthOfLongestSubstring(string s) {

            if (s.empty()) return 0;
            s = ' ' + s;
            int n = s.length();
            vector<int> len(n, 0);
            unordered_map<char, int> map;
            for (int i = 1; i < n; i++) {//开空间后正常访问，不用\<=
                if (map.count(s[i])) {
                    len[i] = min(i - map[s[i]], len[i - 1] + 1);
                }
                else len[i] = len[i - 1] + 1;
                map[s[i]] = i;
            }
            return *max_element(len.begin(), len.end());
        }
    };
    ```

解法二：滑动窗口，很多解释都是这个，就不多说了，直接看代码也能明白

??? "滑动窗口"

    ```cpp
    class Solution {
    public:
        int lengthOfLongestSubstring(string s) {
            if (s.size() == 0)
                return 0;
            unordered_map<char, int> cnt;

            int anw = 0;
            int l = 0, r = 0;
            while (r < s.size()) {
                cnt[s[r]]++;
                while (cnt[s[r]] > 1) {
                    cnt[s[l]]--;
                    l++;
                }
                anw = max(anw, r - l + 1);
                r++;
            }
            return anw;
        }
    };
    ```

### 229 多数元素2
- 如果已经出现了，次数加一
- 如果没出现
    - 桶还能放，就放到桶里
    - 没有多余的桶，所有的次数减一

可能有出现在数组靠后的元素，会占到桶里，所以重新统计一次（桶的个数多于 1）

桶的个数：假如说要找超过 `n/k` 个的元素，桶的个数就是 `k-1` 。假如取 `x` 个， `x*(n/k) <= n  ---> x <= k` ，又因为超过 `n/k` ，所以取 `k-1`

??? "slove"

    ```cpp
    class Solution {
    public:
        //first 存数字，second 存出现次数
        pair<int, int> anw[2];
        int cnt = 0;

        void write(int x) {
            if (anw[0].first == x ) {
                anw[0].second++;
                return;
            }
            else if (anw[1].first == x ) {
                anw[1].second++;
                return;
            }
            else if (anw[0].second == 0) {
                anw[0].first = x;
                anw[0].second = 1;
                return;
            }
            else if (anw[1].second == 0) {
                anw[1].first = x;
                anw[1].second = 1;
                return;
            }
            else {
                anw[0].second--;
                anw[1].second--;
            }
        }
        vector<int> majorityElement(vector<int> & nums) {
            anw[0].first = anw[1].first = INT_MAX;
            for (auto x : nums) {
                write(x);
            }
            vector<int> res;
            anw[0].second = anw[1].second = 0;
            for (auto x : nums) {
                if (x == anw[0].first)
                    anw[0].second++;
                else if (x == anw[1].first)
                    anw[1].second++;
            }
            if (anw[0].second > nums.size() / 3)
                res.push_back(anw[0].first);
            if (anw[1].second > nums.size() / 3)
                res.push_back(anw[1].first);
            return res;
        }
    };
    ```

### 739 每日温度
2024-01-03 看到公众号发的，当时有个朦胧的思路，想到用单调栈，然后发现力扣曾经交过这个题，复习一下

开始是这么想的，用 `[1, 5, 4, 2, 7]` 试了一下，答案是 `[1, 3, 2, 1, 0]` ，从后往前来，（最后一个元素的答案一定是 0），7 先压栈，2 比 7 小，答案是 1，4 的下一个气温应该是 7，但是栈里现在有个 2 ，不是我们想要的就弹出，所以总结出，当前指向元素比栈顶小就入栈，比栈顶大就不断弹栈，如果等于栈顶呢，举个例子就好了 `[2, 2, 7]`，很容易就得出应该弹栈，因为我们找的是比当前元素大的气温。

??? "倒序"

    ```cpp
    class Solution {
    public:
        vector<int> dailyTemperatures(vector<int> &temperatures) {
            stack<int> st;
            int n = temperatures.size();
            vector<int> anw(n);

            for (int i = n - 1; i >= 0; i--) {
                while (!st.empty() && temperatures[i] >= temperatures[st.top()]) {
                    st.pop();
                }
                if (!st.empty())
                    anw[i] = st.top() - i;
                else anw[i] = 0;
                
                st.push(i);
            }
            return anw;
        }
    };
    ```

然后看了一下答案，他是正序处理的，如果当前元素比栈顶大，说明应该用当前元素更新之前想要的答案，（就是找了高的气温），反之直接入栈

??? "正序"

    ```cpp
    class Solution {
    public:
        vector<int> dailyTemperatures(vector<int> &temperatures) {
            stack<int> st;
            int n = temperatures.size();
            vector<int> anw(n);

            for (int i = 0; i < n; i++) {
                while (!st.empty() && temperatures[i] > temperatures[st.top()]) {
                    int top = st.top();
                    anw[top] = i - top;
                    st.pop();
                }
                st.push(i);
            }
            return anw;
        }
    };
    ```

事后想一想，其实都差不多，甚至正序更直观，符合题的操作说法。正序处理相当于是存已知的低温，用更高的气温更新过去。逆序相当于是存已知的高温，用低温去找高温。

[洛谷原题](https://www.luogu.com.cn/problem/P5788)第一个题解这么看更好理解

```txt
-------------`
     2  4  7 |
-------------          
```

做出来了，feel good🥰

### 124 二叉树中的最大路径和

刚开始想到类似后序遍历，找到左子树和右子树的最大路径和，再和根加一起，方向是对的，但应该不是返回最大路径和，而是以 `root.left/right` 为一端的结果

正解：处理当前节点，找到左子树和右子树的最大路径和 `left,right` 。动态更新 `anw` （看 `left, right` 有没有贡献，有贡献就加到 `sum`）。 另外 dfs 找的是子树（假如是左子树）为一端的结果。直接看代码可能更好理解

??? "slove 124"

    ```cpp
    class Solution {
    public:
        int anw = -0x3f3f3f;
        int maxPathSum(TreeNode * root) {
            dfs(root);
            return anw;
        }
        
        int dfs(TreeNode * root) {
            if (root == nullptr)
                return 0;

            int val = root->val;
            int left = dfs(root->left);
            int right = dfs(root->right);
            if (left > 0)
                val += left;
            if (right > 0)
                val += right;
            anw = max(anw, val);

            return max(root->val, max(left, right) + root->val);
        }
    };

    ```

### 232 两个栈实现队列
push 的时候好说，在 pop 的时候没有必要全倒腾，只有在输出栈为空的时候再倒腾就行了

??? "solve"

    ```cpp

    class MyQueue {
    private:
        stack<int> inSt;
        stack<int> outSt;
    public:
        MyQueue() {
        }

        void push(int x) {
            inSt.push(x);
        }

        int pop() {
            if (outSt.empty()) {
                while (!inSt.empty()) {
                    outSt.push(inSt.top());
                    inSt.pop();
                }
            }
            int x = outSt.top();
            outSt.pop();
            return x;
        }

        int peek() {
            if (outSt.empty()) {
                while (!inSt.empty()) {
                    outSt.push(inSt.top());
                    inSt.pop();
                }
            }
            return outSt.top();
        }

        bool empty() {
            return inSt.empty() && outSt.empty();
        }
    };
    ```

### 第K大的数

[链接](https://leetcode.cn/problems/kth-largest-element-in-an-array/description/)

- 想要达到 $O(n)$ 时间，就得从快排变形。
- 第K大的数正好是下标为size-k
- 一次快排相当于把一个数放到对应位置，那就找哪一次放好了的下标正好是要求的
> 没做出来时痛苦万分，~~~抄完了~~~ 学会了之后觉得就应该这么写😥

??? slove
    ```C++
    class Solution {
    public:
    int findKthLargest(vector<int> &nums, int k) {
        return quicksort(nums,0,nums.size()-1,nums.size()-k);
    }

    int quicksort(vector<int> &nums, int l, int r, int k) {
        if (l == r) return nums[k];
        int i = l - 1, j = r + 1, mid = nums[l + r >> 1];
        while (i < j) {
            do i++; while (nums[i] < mid);
            do j--; while (nums[j] > mid);
            if (i < j) swap(nums[i], nums[j]);
        }
        if (k <= j) return quicksort(nums, l, j, k);
        else return quicksort(nums, j + 1, r, k);
    }
    };
    ```

从这个题谈开，假如要找[第K小的数](https://www.acwing.com/problem/content/description/788/)可以说每次快排结束能获得左边≤a[j]的结果，右边大于等于a[j+1]的结果，问题在于是怎么判断下一次的区间（我个人觉得看成下标好理解）

1. k看成长度，比较到lo的距离
```cpp
if (j - lo + 1 >= k)
    return wqsort(a, lo, j, k);
else
    return wqsort(a, j + 1, hi, k - (j + 1 - lo));
```
2. k看成下标
```cpp
if (k<=j)
    return wqsort(a, lo, j, k);
else
    return wqsort(a, j + 1, hi, k);
```

=== "下标"

    ```cpp
    #include <iostream>

    using namespace std;
    const int N = 100010;

    int wqsort(int a[], int lo, int hi, int k) {
        if (lo >= hi)
            return a[k];//a[lo] also OK

        int i = lo - 1, j = hi + 1, mid = a[lo + hi >> 1];
        while (i < j) {
            do
                i++;
            while (a[i] < mid);
            do
                j--;
            while (a[j] > mid);
            if (i < j)
                swap(a[i], a[j]);
        }
        if (k <= j)
            return wqsort(a, lo, j, k);
        else
            return wqsort(a, j + 1, hi, k);
    }

    int main() {
        int n;
        cin >> n;
        int k;
        cin >> k;
        int a[N];
        for (int i = 0; i < n; i++)
            cin >> a[i];
        cout << wqsort(a, 0, n - 1, k - 1);//转成下标
        return 0;
    }
    ```

=== "长度"

    ```cpp
    #include <iostream>

    using namespace std;
    const int N = 100010;

    int wqsort(int a[], int lo, int hi, int k) {
        if (lo >= hi)
            return a[lo];

        int i = lo - 1, j = hi + 1, mid = a[lo + hi >> 1];
        while (i < j) {
            do
                i++;
            while (a[i] < mid);
            do
                j--;
            while (a[j] > mid);
            if (i < j)
                swap(a[i], a[j]);
        }
        if (j - lo + 1 >= k)
            return wqsort(a, lo, j, k);
        else
            return wqsort(a, j + 1, hi, k - (j + 1 - lo));
    }

    int main() {
        int n;
        cin >> n;
        int k;
        cin >> k;
        int a[N];
        for (int i = 0; i < n; i++)
            cin >> a[i];
        cout << wqsort(a, 0, n - 1, k);
        return 0;
    }
    ```

### 两个有序数组第K大元素
这里指的是排列好后，下标为`k-1`的元素，不是去重后的第K大

1. 最基础的：两个数组合并到一个大数组，排序，返回即可
2. 改进一点：不用开太多的空间，用两个指针分别指向起始位置，移动就加一，一直加到等于K

??? "双指针"

    ```cpp
    int kthElement(vector<int> arr1, vector<int> arr2, int array1len, int array2len, int k) {
        int l1 = 0, l2 = 0;
        int cnt = 0;
        int tem = 0;
        while (l1 < array1len && l2 < array2len) {
            if (arr1[l1] < arr2[l2]) {
                tem = arr1[l1];
                l1++;
            }
            else {
                tem = arr2[l2];
                l2++;
            }
            cnt++;
            if (cnt == k) return tem;
        }
        while (cnt < k && l1 < array1len) {
            tem = arr1[l1++];
            cnt++;
        }
        while (cnt < k && l2 < array2len) {
            tem = arr2[l2++];
            cnt++;
        }
        if (cnt == k)return tem;
        return -1;
    }

    ```

**最优版**：可以发现这么一个规律 `l1 <= r2, l2 <= r1`

![cut](https://lh3.googleusercontent.com/3kUSML0zVJzEHWT5LVrLWWuzEJPyedVbFX0ykHI08Q9IzP8lon0Sl5m-qwj2b9Y8X9yCp6fbc8aSxb-3tmM7FmYPt8Q35FWlC3RK8adqo8ArIiH3oR19BqCSTvCDkC1Ujr-ap6cC)

只要找到符合上边条件并且元素个数正好为 K 的情况， `max(l1,l2)` 就是答案

- 假定 `arr1len <= arr2len` ，用二分确定从 arr1 取几个元素(arr2 取 `k- cut1` 个)

假如 `arr1len = 5, arr2len = 7`, `lo` 是 arr1 最少取的个数， `hi` 是最多取的个数

```txt
k = 3, lo = 0, hi = 3
k = 6, lo = 0, hi = 5
k = 11, lo = 4, hi = 5
```

arr1 取几个元素和 arr2len 有关，arr1最少取 `max(0, k-arr2len)`， arr1最多取 `min(k, arr1len)` 个

- 假如 arr1 取了0个元素，那么为了方便判断， `l1=INT_MIN`
- 假如 arr1 取了 arr1len 个元素，那么为了方便判断， `l1=INT_MAX`

```cpp
int kthElement(vector<int> &arr1, vector<int> &arr2, int arr1len, int arr2len, int k) {
    if (arr1len > arr2len) {
        return kthElement(arr2, arr1, arr2len, arr1len, k);
    }
    int lo = max(0, k - arr2len), hi = min(k, arr1len);
    while (lo <= hi) {
        int cut1 = lo + hi >> 1;
        int cut2 = k - cut1;
        int l1 = cut1 == 0 ? INT_MIN : arr1[cut1 - 1];
        int l2 = cut2 == 0 ? INT_MIN : arr2[cut2 - 1];
        int r1 = cut1 == arr1len ? INT_MAX : arr1[cut1];
        int r2 = cut2 == arr2len ? INT_MAX : arr2[cut2];

        if (l1 <= r2 && l2 <= r1) {
            return max(l1, l2);
        }
        else if (l1 > r2)
            hi = cut1 - 1;
        else lo = cut1 + 1;
    }
    return 1;
}
```

如果没有理解，可以参考以下资料，视频讲的比较清楚

- [印度老哥的视频讲解](https://takeuforward.org/data-structure/k-th-element-of-two-sorted-arrays/#google_vignette)
- [有关cut的讲解](https://blog.csdn.net/hk2291976/article/details/51107778?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-51107778-blog-119826156.235%5Ev40%5Epc_relevant_3m_sort_dl_base4&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-51107778-blog-119826156.235%5Ev40%5Epc_relevant_3m_sort_dl_base4&utm_relevant_index=2)

### 环形链表2

[题目链接](https://leetcode.cn/problems/linked-list-cycle-ii/description/)

[完整版题解](https://leetcode.cn/problems/linked-list-cycle-ii/solutions/12616/linked-list-cycle-ii-kuai-man-zhi-zhen-shuang-zhi-/)

- 设有a个节点（不含环的起点），环内有b个节点
- 当第一次相遇时，

$$
\begin{aligned}
fast &=2*low \\
fast &=low+n*b \\
fast &=2nb \\
low  &=nb 
\end{aligned}
$$


- 所有从头开始走到环的起点都是 $a+Nb步$
- 所以low再走a步就到起点，那么让快指针重新指向头，一次一步走a步，两者就会重合

??? solve
    ```C++
    class Solution {
    public:
    ListNode *detectCycle(ListNode *head) {
        ListNode *low = head;
        ListNode *fast = head;
        bool ff = false;
        while (fast != nullptr && fast->next != nullptr) {
            fast = fast->next->next;
            low = low->next;
            if (fast == low) {
                ff = true;
                break;
            }
        }
        if (ff) {
           fast=head;
            while (fast!=low){
                fast=fast->next;
                low=low->next;
            }
            return low;
        }
        return nullptr;

    }
    };
    ```



### 寻找重复数 要求O(1)空间

[transport](https://leetcode.cn/problems/find-the-duplicate-number/description/)

以[1,3,4,2,2]为例，如果有相同数字，相当于会存在一个环

**核心**：
下标和内容一起做指向

| 下标 | 0 | 1 | 3 | 2 | 4       |
|------|---|---|---|---|---------|
| 内容 | 1 | 3 | 2 | 4 | 2(成环) |
| 节点 | 1 | 3 | 2 | 4 | 2       |

然后就和[环形链表2](https://leetcode.cn/problems/linked-list-cycle-ii/description/)一个做法，判环找入口

??? slove
    ```C++
    class Solution {
    public:
        int findDuplicate(vector<int>& nums) {
            int low=0,fast=0;
            //go 1step 2step
            low=nums[low];
            fast=nums[nums[fast]];
            while (low!=fast){
                low=nums[low];
                fast=nums[nums[fast]];
            }
            //fast goto begin node
            fast=0;
            while (low!=fast){
                low=nums[low];
                fast=nums[fast];
            }
            return fast;
            
        }
    };
    ```


### [最短无序连续子数组](https://leetcode.cn/problems/shortest-unsorted-continuous-subarray/)

**双指针**

- 找出升序，降序的区间，中间就是无序。
- 希望中间的值 `x>Lmax&&x<Rmin` ，反过来说，当 `x<Lmax||x>Rmin` 就应该调整左右端点
- 细节部分
- 为了方便调整到数组开始和结尾，用1e5+10和-1e5-10进行设置
- 为什么无序区间的数字开始从**L**找？如果从**L+1**开始，反例是 `1, 3, 2, 2, 2`

??? "双指针"
    ```cpp

    class Solution {
    public:
        int findUnsortedSubarray(vector<int> &nums) {
            if (nums.size() == 1) return 0;

            int l = 0, r = nums.size() - 1;
            while (l < r && nums[l] <= nums[l + 1]) l++;
            while (l < r && nums[r] >= nums[r - 1]) r--;

            int lmaxval = nums[l], rminval = nums[r];
            if (l == r) return 0;
    
            int i = l + 1;
            for (int k = l ; k < r; ++k) {
                if (nums[k] < lmaxval) {
                    while (l >= 0 && nums[k] < lmaxval) {
                        l--;
                        if (l < 0) lmaxval = -1e5 - 10;
                        else lmaxval = nums[l];
                    }
                
                }
                if (nums[k] > rminval) {
                    while (r < nums.size() && nums[k] > rminval) {
                        r++;
                        if (r >= nums.size())
                            rminval = 1e5 + 10;
                        else rminval = nums[r];
                    }
                
                }
            }

            return r - l - 1;
        }
    };
    ```

**一次遍历** [传送门](https://leetcode.cn/problems/shortest-unsorted-continuous-subarray/solutions/422614/si-lu-qing-xi-ming-liao-kan-bu-dong-bu-cun-zai-de-/comments/1194164)

先只考虑中段数组，设其左边界为L，右边界为R：

`nums[R]` 不可能是 `[L，R]` 中的最大值（否则应该将 `nums[R]` 并入右端数组）

`nums[L]` 不可能是`[L,R]`中的最小值（否则应该将 `nums[L]` 并入左端数组）

很明显:

 `[L,R]` 中的最大值 等于 `[0，R]` 中的最大值，设其为 max

 `[L,R]` 中的最小值 等于 `[L， nums.length-1]`中的最小值，设其为 min

那么有：

`nums[R] < max < nums[R+1] < nums[R+2] < ...`  所以说，从左往右遍历，最后一个小于max的为右边界

`nums[L] > min > nums[L-1] > nums[L-2] > ... ` 所以说，从右往左遍历，最后一个大于min的为左边界

??? "一次遍历"
    ```cpp
    class Solution {
    public:
        int findUnsortedSubarray(vector<int> &nums) {

    
            int min = nums[nums.size() - 1], max = nums[0];
            int end = -1, begin = 0;
            //end和begin的初值不重要，让end-bigin+1=0即可
            for (int i = 0; i < nums.size(); ++i) {
                if (nums[i] < max)
                    end = i;
                else max = nums[i];

                if (nums[nums.size() - 1 - i] > min)
                    begin = nums.size() - 1 - i;
                else min = nums[nums.size() - 1 - i];
            }
            return end - begin + 1;
        }
    };
    ```


### [最长上升子序列](https://www.acwing.com/problem/content/description/897/)

1. dp $O(n^2)$ , `dp[i]=max(dp[i],dp[j]+1) when a[i]>a[j],`
2. dp+贪心，每次找 `x<=anw[i]`的左端点更新
3. 记忆化搜索

=== "dp On2"

    ```cpp
    #include "bits/stdc++.h"

    using namespace std;

    int main() {
        int n;
        cin >> n;
        vector<int> a(n);
        for (int i = 0; i < n; i++)
            cin >> a[i];
        vector<int> dp(n, 1);
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (a[i] > a[j])
                    dp[i] = max(dp[i], dp[j] + 1);
            }
        }
        cout << *max_element(dp.begin(), dp.end());
        return 0;
    }
    ```

=== "dp+贪心"

    ```cpp
    #include "bits/stdc++.h"

    using namespace std;

    void check(int x, vector<int> &anw) {
        int l = 0, r = anw.size() - 1;
        while (l < r) {
            int mid = l+r>>1;
            if (anw[mid] <x)l = mid+1;
            else r = mid;
        }

        anw[r] = x;
    }

    int main() {
        int n;
        cin >> n;
        vector<int> a(n);
        for (int i = 0; i < n; i++)
            cin >> a[i];
        vector<int> anw;
        anw.push_back(a[0]);
        for (int i = 1; i < n; i++) {
            if (anw[anw.size() - 1] < a[i])
                anw.push_back(a[i]);
            else check(a[i], anw);
        }
        cout << anw.size();
        return 0;
    }
    ```

### [数组中的逆序对](https://www.acwing.com/problem/content/description/61/)

- 归并排序，注意循环的边界是 `l r`不是 **0**
- 套模板会空间多一点但是直观，优化一下相当于不停在求子问题

=== "开空间较多"

```cpp
class Solution {
public:
    int inversePairs(vector<int> &nums) {
        if(nums.size()==0) return 0;
        int anw = mergesort(nums, 0, nums.size() - 1);
        return anw;
    }

    int mergesort(vector<int> &nums, int l, int r) {
        if (l == r) return 0;
        int mid = l + r >> 1;
        int cnt = 0;
        cnt += mergesort(nums, l, mid);
        cnt += mergesort(nums, mid + 1, r);

        int m = mid, n = r, id = r;
        
        vector<int> anw(nums.size());
        
        while (m >= l && n > mid) {
            if (nums[n] >= nums[m]) anw[id--] = nums[n--];
            else cnt+=n-mid, anw[id--] = nums[m--];
        }
        while (m >= l) anw[id--] = nums[m--];
        while (n > mid)anw[id--] = nums[n--];
        
        id = l;
        while (id <= r)
            nums[id] = anw[id], id++;
            
        return cnt;
    }
};
```

=== "On空间"

```cpp
class Solution {
public:
    int inversePairs(vector<int> &nums) {
        if (nums.size() == 0) return 0;
        vector<int> tem = nums;
        int anw = mergesort(nums, 0, nums.size() - 1, tem);
        return anw;
    }

    int mergesort(vector<int> &nums, int l, int r, vector<int> &tem) {
        if (l == r) {
            tem[l] = nums[l];
            return 0;
        }
        int mid = l + r >> 1;
        int cnt = 0;
        cnt += mergesort(tem, l, mid, nums);
        cnt += mergesort(tem, mid + 1, r, nums);

        int m = mid, n = r, id = r;
        while (m >= l && n >= mid+1) {
            if(nums[m]>nums[n]) {
                tem[id--]=nums[m--];
                cnt+=n-mid;
            }
            else tem[id--]=nums[n--];
        }
        while (m >= l) tem[id--] = nums[m--];
        while (n > mid)tem[id--] = nums[n--];

        return cnt;
    }
};
```

### [编辑距离](https://leetcode.cn/problems/edit-distance/)

[题解](https://leetcode.cn/problems/edit-distance/solutions/2468072/dai-ma-sui-xiang-lu-72-bian-ji-ju-chi-by-or3j/)

可以优化成O(M)

??? "slove"

    ```cpp
    class Solution {
    public:
        void printmarix(vector<int> source) {
            for (auto x : source)
                cout << x << ' ';
            cout << endl;
        }

        void printVect(vector<vector<int>> dp) {
            for (auto x : dp) {
                for (auto y : x)
                    cout << y << ' ';
                cout << endl;
            }
        }

        int minDistance(string source, string dest) {
            vector<int> dp(dest.size() + 1, 0);


            for (int i = 0; i <= dest.size(); i++)
                dp[i] = i;

            for (int i = 1; i <= source.size(); i++) {
                int tem = dp[0];
                dp[0] = i;
                for (int j = 1; j <= dest.size(); j++) {
                    int pre_dp_j = dp[j];
                    if (source[i - 1] == dest[j - 1])
                        dp[j] = tem;
                    else
                        dp[j] = min(tem, min(dp[j - 1], dp[j])) + 1;

                    tem = pre_dp_j;
                }
                //  printmarix(dp);
            }
            return dp[dest.size()];
        }

        int minDistance_corr(string source, string dest) {
            vector<vector<int>> dp(source.size() + 1, vector<int>(dest.size() + 1, 0));

            for (int i = 0; i <= source.size(); i++)
                dp[i][0] = i;
            for (int i = 0; i <= dest.size(); i++)
                dp[0][i] = i;

            for (int i = 1; i <= source.size(); i++)
                for (int j = 1; j <= dest.size(); j++) {
                    if (source[i - 1] == dest[j - 1])
                        dp[i][j] = dp[i - 1][j - 1];
                    else
                        dp[i][j] = min(min(dp[i - 1][j - 1], dp[i - 1][j]), dp[i][j - 1]) + 1;
                }
            //  printVect(dp);
            return dp[source.size()][dest.size()];
        }
    };

    ```

## interesting question
### Decimal dominants
Given an array with n keys, design an algorithm to find all values that occur more than  n/10 times. The expected running time of your algorithm should be linear. [题解](https://www.cnblogs.com/evasean/p/7273857.html) 这个让我联想到莫尔投票法的一个题[力扣169](https://leetcode.cn/problems/majority-element/description/)

two sum with link node

??? "solve"

    ```cpp
    class Solution {
    public:
        ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
            ListNode* head = new ListNode(0);
            ListNode* tem = head;
            int adding = 0;
            while (l1 || l2) {
                int x = 0, y = 0;
                if (l1) x = l1->val;
                if (l2) y = l2->val;
                int sum = (x + y + adding) % 10;
                adding = (x + y + adding) / 10;
                ListNode* ne = new ListNode(sum);
                tem->next = ne;
                tem = tem->next;
                if (l1)
                    l1 = l1->next;
                if (l2)
                    l2 = l2->next;
            }
            if (adding) {
                ListNode* ne = new ListNode(adding);
                tem->next = ne;
            }
            tem = head->next;
            delete head;
            return tem;
        }
    };
    ```

### [The Dutch national flag](https://en.wikipedia.org/wiki/Dutch_national_flag_problem)
sort an array of some 0,1,2 in O(n) 

- [0, i-1] < midElement
- [i, j-1] = midElement
- [j, k] unsorted
- [k+1, end] >midElement


??? "solve"

    ```cpp
    void dutchFlag(vector<int>&todo){
        int N=todo.size();
        int low=0,mid=0,high=N-1;
        while (mid<=high){
            if(todo[mid]==0){
                swap(todo[low],todo[mid]);
                low++;
                mid++;
            }
            else if(todo[mid]==2){
                swap(todo[mid],todo[high]);
                high--;
            }
            else mid++;
        }
    }
    ```


### Merging with smaller auxiliary array
given an array[2n], which is sorted from a[0] to a[n], and sorted from a[n+1] to a[2n]. you need to sort the entire array with O(n) space

solve:
1. copy the first part to auxiliary array
2. merge auxiliary array and the second part of original array 

??? "solve"

    ```cpp
    #include <vector>
    #include <algorithm>
    #include <iostream>

    using namespace std;

    void merge(vector<int> &a) {
        vector<int> aux = vector<int>(a.size() / 2);
        int half = a.size() / 2;
        for (int i = 0; i < half; i++)
            aux[i] = a[i];

        int lo = 0, hi = half;
        int k = 0;
        
        while (lo < half && hi < a.size()) {
            if (aux[lo] < a[hi])
                a[k++] = aux[lo++];
            else a[k++] = a[hi++];
        }
        while (lo < half) a[k++] = aux[lo++];
        while (hi < a.size()) a[k++] = a[hi++];

    }

    void test() {
        vector<int> a = {6, 7, 8, 9, 10, 1, 2, 3, 4, 5};
        vector<int> expect = a;
        std::sort(expect.begin(), expect.end());

        merge(a);
        for (auto x: a)
            cout << x << ' ';

        if (a == expect)
            cout << "yes";
        else
            cout << "NO";
    }

    int main() {
        test();
        return 0;
    }
    ```

### Taxicab numbers
find items like `a^3+b^3=c^3+d^3`

可以这么想，看成横纵 1 到 n 的矩阵，里边填写立方和。上三角和下三角的元素一样所以只考虑上三角。

1. 遍历所有横纵坐标，用哈希表，出现过的立方和就输出一下，没出现过的就存起来
2. 用堆存立方和（也可以是优先队列，我这里用最小堆，相当于立方和从小到大，最大堆也行，相当于从大到小）堆不空就反复尝试。另外用堆拓展的时候只往一个方向拓展，用两个就不对

```cpp
#include <unordered_map>
#include "iostream"
#include "vector"
#include "queue"

using namespace std;

class taxinum {

public:
    int a, b, sum;

    taxinum(int _a, int _b) : a(_a), b(_b), sum(a * a * a + b * b * b) {}

    bool operator==(taxinum other) const {
        return this->sum == other.sum;
    }

    bool operator<(taxinum other) const {
        return this->sum < other.sum;
    }

    bool operator>(taxinum other) const {
        return this->sum > other.sum;
    }

    friend ostream &operator<<(ostream &os, const taxinum &t) {
        os << t.a << '+' << t.b << '=' << t.sum;
        return os;
    }


};

void testMinheap() {
    priority_queue<taxinum, vector<taxinum>, greater<taxinum>> queue1;

    int n = 30;
    for (int i = 1; i <= n; i++)
        queue1.push(taxinum(i, i));

    taxinum oldPair(1, 1);
    while (!queue1.empty()) {
        taxinum newPair = queue1.top();
        queue1.pop();

        if (newPair.sum == oldPair.sum)
            cout << oldPair << "-----" << newPair << endl;
        if (newPair.b < n)
            queue1.push(taxinum(newPair.a, newPair.b + 1));

        oldPair = newPair;
    }

}

void testHashmap() {
    int n = 30;
    unordered_map<int, pair<int, int>> table;
    for (int i = 1; i <= n; i++)
        for (int j = i; j <= n; j++) {
            taxinum t(i, j);
            int sum = t.sum;
            if (table.contains(sum)) {
                cout << i << ' ' << j << ' ' << table[sum].first << ' ' << table[sum].second << endl;
            }
            else {
                table[sum] = {i, j};
            }
        }

}

void testMaxheap() {
    priority_queue<taxinum, vector<taxinum>, less<taxinum>> queue1;

    int n = 30;
    for (int i = 1; i <= n; i++)
        queue1.push(taxinum(i, i));

    taxinum oldPair(1, 1);
    while (!queue1.empty()) {
        taxinum newPair = queue1.top();
        queue1.pop();

        if (newPair.sum == oldPair.sum)
            cout << oldPair << "-----" << newPair << endl;
        //一个方向就够了
        if (newPair.b > 0)
            queue1.push(taxinum(newPair.a, newPair.b - 1));
//        if (newPair.a > 0)
//            queue1.push(taxinum(newPair.a - 1, newPair.b));

        oldPair = newPair;
    }

}

int main() {
    testMaxheap();
    testHashmap();
    return 0;
}
```

## codeforces

### [lakes](https://codeforces.com/contest/1829/problem/E)

??? "lakes"

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

??? "solve"

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

