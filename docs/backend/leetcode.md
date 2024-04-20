---
comment: true
---

### 倒水
有一个容量为8, 5, 3的桶，其中8为满，5和3为空，只能倒满和倒空，想一个办法得到4的水

bfs，注意搜过的状态不在搜，但是为什么这个类只能存到上一步不能存到上上步，待解决

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

### 11 盛最多水的容器
双指针移动短板，[正确性证明](https://leetcode.cn/problems/container-with-most-water/solutions/11491/container-with-most-water-shuang-zhi-zhen-fa-yi-do)

```cpp
class Solution {
public:
    int maxArea(vector<int> &height) {
        int anw = 0;
        int left = 0, right = height.size() - 1;
        while (left < right) {
            int area = min(height[left], height[right]) * (right - left);
            anw = max(anw, area);
            if (height[left] < height[right]) left++;
            else right--;
        }
        return anw;
    }
};
```

### 15 三数之和
先排序，双指针，从 i+1 和 end 找，问题在于去重，比如用例 `[-1,0,1,2,-1,-4]`, `[-2,0,0,2,2]`；由于找的时候是从 `[i+1, end]` 找，第一个判定就是 `nums[i] == nums[i-1]`,网上的题解都是 left 和下一位比是否重复，最后在 left++，我觉得这样很容易忘 left++，不如直接用两个变量 left_used, right_used 记录，不用最后再 left++，这样好一些。另外还可以提前判定是否结束，`nums[i]>0`

难度不是很大，但是细节比较多

=== "变量"

    ```cpp
    class Solution {
    public:
        void test() {
            vector<int> origin = {-2, 0, 0, 2, 2};
            auto anw = threeSum(origin);
            for (auto x : anw) {
                for (auto y : x)
                    cout << y << ' ';
                cout << endl;
            }
        }

        vector<vector<int>> threeSum(vector<int>& nums) {
            vector<vector<int>> anw;
            sort(nums.begin(), nums.end());

            for (int i = 0; i < nums.size(); ++i) {
                if (nums[i] > 0)
                    break;
                if (i >= 1 && nums[i - 1] == nums[i])
                    continue;

                int left = i + 1, right = nums.size() - 1;

                while (left < right) {
                    int left_use = nums[left], right_use = nums[right];
                    int sum = left_use + right_use;
                    if (sum > -1 * nums[i])
                        right--;
                    else if (sum < -1 * nums[i])
                        left++;
                    else {
                        anw.emplace_back(vector<int>{nums[i], left_use, right_use});
                        while (left < right && nums[left] == left_use)
                            left++;
                        while (left < right && nums[right] == right_use)
                            right--;
                    }
                }
            }
            return anw;
        }
    };
    ```

=== "不用变量"

    ```cpp
    class Solution {
    public:
        void test() {
            vector<int> origin = {-2, 0, 0, 2, 2};
            auto anw = threeSum(origin);
            for (auto x: anw) {
                for (auto y: x)
                    cout << y << ' ';
                cout << endl;
            }
        }

        vector<vector<int>> threeSum(vector<int> &nums) {
            vector<vector<int>> anw;
            sort(nums.begin(), nums.end());

            for (int i = 0; i < nums.size(); ++i) {
                if (nums[i] > 0) break;
                if (i >= 1 && nums[i - 1] == nums[i])
                    continue;

                int left = i + 1, right = nums.size() - 1;

                while (left < right) {
                    int sum = nums[left] + nums[right];
                    if (sum > -1 * nums[i])
                        right--;
                    else if (sum < -1 * nums[i])
                        left++;
                    else {
                        anw.emplace_back(vector<int>{nums[i], nums[left], nums[right]});
                        while (left < right && nums[left] == nums[left + 1])
                            left++;
                        left++;
                        while (left < right && nums[right] == nums[right - 1])
                            right--;
                        right--;
                    }
                }
            }
            return anw;
        }
    };
    ```


### 17 电话号码组合
典型回溯

```cpp
class Solution {
public:
    unordered_map<char, vector<char>> orders;
    vector<string> anw;
    string res;

    vector<string> letterCombinations(string digits) {
        if(digits.empty()) return vector<string>{};
        setting();
        dfs(digits, 0);
        return anw;
    }

    void dfs(const string& digits, int now_index) {
        if (now_index == digits.size()) {
            anw.push_back(res);
        }
        for (int i = 0; i < orders[digits[now_index]].size(); i++) {
            res.push_back(orders[digits[now_index]][i]);
            dfs(digits, now_index + 1);
            res.pop_back();
        }
    }

    void setting() {
        orders['2'] = {'a', 'b', 'c'};
        orders['3'] = {'d', 'e', 'f'};
        orders['4'] = {'g', 'h', 'i'};
        orders['5'] = {'j', 'k', 'l'};
        orders['6'] = {'m', 'n', 'o'};
        orders['7'] = {'p', 'q', 'r', 's'};
        orders['8'] = {'t', 'u', 'v'};
        orders['9'] = {'w', 'x', 'y', 'z'};
    }
};
```

### 22 括号生成
最开始想的是在当前基础前边加 `()`，后边加 `()`，包裹当前 `(cur)`。然后错了，因为少了 `(())(())` 这种。

还得是一次拼接一个 `(` 或者 `)`

```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        dfs(n, n);
        return anw;
    }

    vector<string> anw;
    string res;

    void dfs(int left_can_use, int right_can_use) {
        if (!left_can_use && !right_can_use) {
            anw.push_back(res);
            return;
        }

        // 一定不存在正确的匹配 )(((((
        if (left_can_use > right_can_use) return; 
        
        if (left_can_use > 0) {
            res.push_back('(');
            dfs(left_can_use - 1, right_can_use);
            res.pop_back();
        }
        if (right_can_use > 0) {
            res.push_back(')');
            dfs(left_can_use, right_can_use - 1);
            res.pop_back();
        }
    }
};
```

### 23 合并k个链表
[题解](https://leetcode.cn/problems/merge-k-sorted-lists/solutions/2384305/liang-chong-fang-fa-zui-xiao-dui-fen-zhi-zbzx)

第一种，每次挑一个答案，如果后面还有节点，有可能是下一个答案，用最小堆存

```cpp
class Solution {
public:
    ListNode *mergeKLists(vector<ListNode *> &lists) {
        auto cmp = [](ListNode *a, ListNode *b) {
            return a->val > b->val;
        };
        priority_queue<ListNode *, vector<ListNode *>, decltype(cmp)> pq;
        for (auto item: lists) {
            if (item) pq.push(item);
        }
        ListNode dummy(0);
        ListNode *cur = &dummy;
        while (!pq.empty()) {
            auto item = pq.top();
            pq.pop();
            if (item->next) { pq.push(item->next); }
            cur->next = item;
            cur = cur->next;
        }
        return dummy.next;
    }
};
```

第二种，类似归并，两两合并有序链表

```cpp
class Solution {
public:
    ListNode *mergeKLists(vector<ListNode *> &lists) {
        if(lists.empty()) return nullptr;
        return mergePart(lists, 0, lists.size() - 1);
    }

    ListNode *mergePart(vector<ListNode *> &lists, int left, int right){
        if (left >= right) return lists[left];
        int mid = left + right >> 1;
        auto leftHead = mergePart(lists, left, mid);
        auto rightHead = mergePart(lists, mid + 1, right);
        return mergeTwoList(leftHead, rightHead);
    }

    ListNode *mergeTwoList(ListNode *a, ListNode *b) {
        ListNode dummy(0);
        ListNode *cur = &dummy;
        while (a && b) {
            if (a->val < b->val) {
                cur->next = a;
                a = a->next;
            }
            else {
                cur->next = b;
                b = b->next;
            }
            cur = cur->next;
        }
        cur->next = a ? a : b;
        return dummy.next;
    }
};
```

### 24 两两交换链表

=== "非递归"

    ```cpp
    class Solution {
    public:
        ListNode* swapPairs(ListNode* head) {
            ListNode* dummy = new ListNode(0);
            dummy->next = head;
            ListNode* n0 = dummy;
            ListNode* n1 = head;
            while (n1 && n1->next) { // 有两个点来互换
                ListNode* n2 = n1->next;
                ListNode* n3 = n2->next;

                n0->next = n2;
                n2->next = n1;
                n1->next = n3;
                n0 = n1;
                n1 = n3;
            }
            return dummy->next;
        }
    };
    ```

=== "递归"

    ```cpp
    class Solution {
    public:
        ListNode * swapPairs(ListNode * head) {
            if (head == nullptr || head->next == nullptr) // 要求有两个点来互换
                return head;

            ListNode * n1 = head;
            ListNode * n2 = n1->next;
            ListNode * n3 = swapPairs(n2->next);
            
            n1->next = n3;
            n2->next = n1;
            return n2;
        }
    };
    ```

### 25 k个一组反转链表
链表操作还是非递归符合直觉

=== "非递归"

    ```cpp
    class Solution {
    public:
        ListNode *reverseKGroup(ListNode *head, int k) {
            ListNode *newHead = new ListNode(0);
            newHead->next = head;
            ListNode *n0 = newHead;
            ListNode *n1 = n0->next;

            while (n0) {
                ListNode *nk = n0;
                for (int cnt = 0; cnt < k; cnt++) {
                    // nk 并不能为空，如果为空说明不足 k 个来反转
                    if (nk->next) nk = nk->next; 
                    else return newHead->next;
                }
                ListNode *nkp1 = nk->next;  // nkp1 是 nk 下一个点 nk plus 1

                n0->next = rev(n1, nullptr, k);
                n1->next = nkp1;

                n0 = n1;
                n1 = nkp1;
            }
            return newHead->next;
        }

        // 正常反转链表
        ListNode *rev(ListNode *now, ListNode *pre, int k) {
            if (now == nullptr || k == 0)
                return pre;
            ListNode *ne = now->next;
            now->next = pre;
            return rev(ne, now, k - 1);
        }
    };
    ```

=== "递归"

    ```cpp
    class Solution {
    public:
        ListNode * reverseKGroup(ListNode * head, int k) {
            if(head == nullptr) return nullptr;
            ListNode * n1 = head;
            ListNode * nkp1 = n1;
            for (int i = 0; i < k; i++) {
                if (nkp1) nkp1 = nkp1->next;
                else return head;
            }

            nkp1 = reverseKGroup(nkp1, k);
            ListNode * nk = rev(head, nullptr,k); // 不能和下一句调换，先因为先反转内部，最后改 n1 指向
            n1->next = nkp1;
            return nk;
        }

        ListNode * rev(ListNode * now, ListNode * pre, int k) {
            if (now == nullptr || k == 0)
                return pre;
            ListNode * ne = now->next;
            now->next = pre;
            return rev(ne, now, k - 1);
        }
    };
    ```

### 31 下一个排列
[题解](https://leetcode.cn/problems/next-permutation/solutions/80560/xia-yi-ge-pai-lie-suan-fa-xiang-jie-si-lu-tui-dao-)

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        if (nums.size() == 1)
            return;
        if (nums.size() == 2) {
            swap(nums[0], nums[1]);
            return;
        }
        int k = nums.size() - 2;
        while (k >= 0 && nums[k] >= nums[k + 1]) // 从右往左找第一个严格升序
            k--;
        if (k >= 0) { // 判断不是全降序 [3,2,1]
            int n = nums.size() - 1;
            while (nums[n] <= nums[k] && n > k)  // 从右往左找第一个严格大于nums[k]
                n--;
            cout << k << ' ' << n;
            swap(nums[n], nums[k]);
        }

        sort(nums.begin() + k + 1, nums.end());
    }
};
```

### 39 组合总和
和 78 子集有点像，都是前边有些不选，从当前开始选，也是回溯

```cpp
class Solution {
public:
    vector<int> res;
    vector<vector<int>> anw;
    int target;
    int now_sum;

    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        this->target = target;
        std::sort(candidates.begin(), candidates.end());
        dfs(candidates, 0);
        return anw;
    }

    void dfs(vector<int>& nums, int begin_index) {
        if (now_sum == target) {
            anw.push_back(res);
            return;
        } else if (now_sum > target || begin_index >= nums.size()) {
            return;
        }

        for (int i = begin_index; i < nums.size(); i++) {
            res.push_back(nums[i]);
            now_sum += nums[i];
            dfs(nums, i); // 这个地方是 i 不是 begin_index
            now_sum -= nums[i];
            res.pop_back();
        }
    }
};
```

### 41 缺失的第一个正数
先给出结论，答案会是在 `[1, nums.size+1]` 中

也就是说只要把大小在 `[1,nums.size]` 的数字放到对应的第几位，最后查一遍就能找到答案

```cpp
class Solution {
public:
    int firstMissingPositive(vector<int> &nums) {
        int sz = nums.size();
        for (int i = 0; i < sz; ++i) {
            while (nums[i] >= 1 && nums[i] <= sz &&
                   nums[i] != nums[nums[i] - 1])
                swap(nums[i], nums[nums[i] - 1]);
        }
        for (int i = 0; i < sz; ++i) {
            if (nums[i] != i+1) return i + 1;
        }
        return sz + 1;
    }
};
```

### 42 接雨水
威名远扬啊，手撕接雨水成为[招聘常态](https://www.nowcoder.com/feed/main/detail/6cc9f0f6b4bf44cca6bbfa520f969c6e)😅，希望不是最后茴香豆的茴有几种写法

[题解](https://leetcode.cn/problems/trapping-rain-water/solutions/9112/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-8)

**按列求**：什么时候本列 i 能放呢，会发现 `[0, i-1],[i+1, end]` 如果两区间都能找到比 i 高的，说明能放，而且能放 `min(a,b) - height[i]` （如果等于 i 的高度相当于放 0）。可以每次都找一遍左边和右边，但是这样浪费时间。之前有的 dp 题如果当前位置没有更好的就放本身。叫法好像是备忘录。可以用这个方法

=== "初始版"

    ```cpp
    class Solution {
    public:
        int trap(vector<int> &height) {
            int anw = 0;
            int sz = height.size();
            for (int i = 0; i < sz; ++i) {
                int i_left_maxheight = height[i];
                for (int k = 0; k < i; ++k)
                    i_left_maxheight = max(i_left_maxheight, height[k]);

                int i_right_maxheight = height[i];
                for (int k = i + 1; k < sz; ++k)
                    i_right_maxheight = max(i_right_maxheight, height[k]);

                anw += min(i_left_maxheight, i_right_maxheight) - height[i];
            }
            return anw;
        }
    };
    ```

=== "改进成dp"

    ```cpp
    class Solution {
    public:
        int trap(vector<int> &height) {
            int anw = 0;
            int sz = height.size();
            vector<int> left_maxheight(sz);
            vector<int> right_maxheight(sz);
            
            left_maxheight[0] = height[0];
            right_maxheight[sz - 1] = height[sz - 1];
            
            // 有大取大，没大取本身
            for (int i = 1; i < sz; ++i) {
                left_maxheight[i] = max(left_maxheight[i - 1], height[i]);
            }
            for (int i = sz - 2; i >= 0; --i) { 
            // i should begin sz-2, not sz-1
                right_maxheight[i] = max(right_maxheight[i + 1], height[i]);
            }
            for (int i = 0; i < sz; ++i) {
                anw += min(left_maxheight[i], right_maxheight[i]) - height[i];
            }
            return anw;
        }
    };

    ```

**按列求双指针**：题解里 dp 优化成双指针没看明白，但是第一个评论很好，和 12 题有异曲同工之妙，但是这个双指针先理解 dp 会更好理解

```cpp
class Solution {
public:
    int trap(vector<int> &height) {
        int anw = 0;
        int sz = height.size();
        int left = 0, right = sz - 1;
        int leftMaxHeight = height[left], rightMaxHeight = height[right];

        left++; // 左右边界不能存
        right--;

        while (left <= right) { // left=right 也是可能的答案
            leftMaxHeight = max(leftMaxHeight, height[left]);
            rightMaxHeight = max(rightMaxHeight, height[right]);
            if (leftMaxHeight < rightMaxHeight) {
                anw += leftMaxHeight - height[left];
                left++;
            }
            else {
                anw += rightMaxHeight - height[right];
                right--;
            }
        }
        return anw;
    }
};
```


**单调栈**：假如当前块比前一个低，说明会有雨水（下标入栈，因为后续会用到水平距离）；假如当前块比之前高，说明之前的雨水应该停下，进行计算。（一行按部分求）

计算过程：先取出比 height[i] 低的高度 bottom，再找 bottom 左侧的高度 leftheight， 在 heigh[i] 和 leftheight 两者取小，乘以水平距离

```txt
        x
x       x
xxxxxxxxx
```

```cpp
class Solution {
public:
    void test() {
        vector<int> height = {0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1};
        cout << trap(height);
    }

    int trap(vector<int> &height) {
        int anw = 0;
        int len = height.size();
        stack<int> stack;
        for (int i = 0; i < len; ++i) {
            while (!stack.empty() && height[i] > height[stack.top()]) {
                int haveWaterIndex = stack.top();
                stack.pop();

                if (stack.empty()) break;

                int possibleHeight = min(height[i], height[stack.top()]);
                int waterHeight = possibleHeight - height[haveWaterIndex];
                int distance = i - stack.top() - 1;
                anw += waterHeight * distance;
            }
            stack.push(i);
        }
        return anw;
    }
};
```

### 48 旋转图像
线性代数：在想我的事情？😋

矩阵转置后做竖直轴对称

```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {

        for(int i=0;i<matrix.size();i++)
            for(int j=i+1;j<matrix[i].size();j++)
                swap(matrix[i][j],matrix[j][i]);

        for(int i=0;i<matrix.size();i++)
            for(int j=0;j<matrix.size()/2;j++)
                swap(matrix[i][j],matrix[i][matrix.size()-1-j]);
    }
};
```

### 51 N皇后
难点主要在怎么判断斜着，发现 y+x 对应右上到左下，y-x 对应从左上到右下，但是 y-x 有可能是负的，就加 n 调成非负，也可以用哈希表

```cpp
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        martix = vector<string>(n, string(n, '.'));
        dfs(0, n);
        return anw;
    }

    vector<vector<string>> anw;
    vector<string> martix;
    bool cols[10] = {false};
    bool left_to_right[20] = {false};
    bool right_to_left[20] = {false};

    void dfs(int row, int n) {
        if (row == n) {
            anw.push_back(martix);
            return;
        }
        for (int col = 0; col < n; col++) {
            if (!cols[col] &&
                !right_to_left[col + row] &&
                !left_to_right[col - row + n]) {

                cols[col] = right_to_left[col + row] = left_to_right[col - row + n] = true;
                martix[row][col] = 'Q';
                dfs(row + 1, n);
                martix[row][col] = '.';
                cols[col] = right_to_left[col + row] = left_to_right[col - row + n] = false;
            }
        }
    }
};
```

### 53 最大子数组和
经典 dp 题。无后效性，我的理解是只看它和它之前的事情，不看后边的，可以化简问题；`dp[i]` 表示以 i 为结尾的最大子数组和，所以 `dp[i]=max(dp[i-1]+nums[i], nums[i])` ，优化空间的话不是 anw=max(anw+nums[i], nums[i]), 因为这样求出来的是 dp[end] ，不是 dp[1..end] 中的最大值。用另一个 sum 记录就好了

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int sum = 0;
        int anw = nums[0];
        const int sz = nums.size();
        for (int i = 0; i < sz; ++i) {
            sum = max(sum + nums[i], nums[i]);
            anw = max(anw, sum);
        }
        return anw;
    }
};
```

### 54 螺旋矩阵
这个好像之前抄过的，现在能写出来了😋，但是空间开的多

```cpp
class Solution {
public:

    int m, n;
    vector<vector<bool>> visited;

    vector<int> spiralOrder(vector<vector<int>> &matrix) {
        m = matrix.size();
        n = matrix[0].size();
        visited = vector<vector<bool>>(m, vector<bool>(n, false));
        vector<int> anw(m * n, 0);
        pair<int, int> move[4];
        move[0] = {0, 1};
        move[1] = {1, 0};
        move[2] = {0, -1};
        move[3] = {-1, 0};

        for (int x = 0, y = 0, cnt = 0, mv = 0; cnt < matrix.size() * matrix[0].size();) {
            visited[x][y] = true;
            anw[cnt++] = matrix[x][y];
            int nx = x + move[mv].first;
            int ny = y + move[mv].second;
            if (isvalid(nx, ny)) x = nx, y = ny;
            else {
                mv = (mv + 1) % 4;
                x += move[mv].first;
                y += move[mv].second;
            }
        }
        return anw;
    }

    bool isvalid(int x, int y) {
        return x >= 0 && x < m && y >= 0 && y < n && !visited[x][y];
    }
};
```

优化，只用 4 个变量维护上下左右的界限，死循环放，只要边界非法说明放完了，返回即可

```cpp
class Solution {
public:

    vector<int> spiralOrder(vector<vector<int>> &matrix) {
        vector<int> anw;
        if (matrix.empty()) return anw;

        int up = 0, down = matrix.size()-1, left = 0, right = matrix[0].size() - 1;
        while (true) {
            for (int i = left; i <= right; i++)anw.push_back(matrix[up][i]);
            if (++up > down) break;

            for (int i = up; i <= down; i++)anw.push_back(matrix[i][right]);
            if (--right < left) break;

            for (int i = right; i >= left; i--) anw.push_back(matrix[down][i]);
            if (--down < up) break;

            for (int i = down; i >= up; i--)anw.push_back(matrix[i][left]);
            if (++left > right) break;
        }
        return anw;
    }
};
```

### 55 跳跃游戏
这个题直接翻译也行，看代码

```cpp
class Solution {
public:
    bool canJump(vector<int> &nums) {
        if (nums.size() == 1) return true;
        
        int canReachMaxIndex = 0;
        for (int i = 0; i < nums.size(); ++i) {
            if (canReachMaxIndex >= i)
                canReachMaxIndex = max(canReachMaxIndex, i + nums[i]);
            else return false;
        }
        return true;
    }
};
```

### 45 跳跃游戏2
上来没想到太好的方法，用暴力也过了，但明显不是好的解法。

```cpp
class Solution {
public:
    int jump(vector<int> &nums) {
        vector<int> step(nums.size(), INT_MAX);
        step[0] = 0;
        int canGoMaxIndex = 0;
        for (int i = 0; i < nums.size(); ++i) {
            if (canGoMaxIndex >= i) {
                canGoMaxIndex = max(canGoMaxIndex, i + nums[i]);
                for (int spread = i + 1; spread <= i + nums[i] && spread < nums.size(); ++spread)
                    step[spread] = min(step[spread], step[i] + 1);
            }
        }
        return step[nums.size() - 1];
    }
};
```

看[题解](https://leetcode.cn/problems/jump-game-ii/solutions/2566727/dai-ma-sui-xiang-lu-leetcode-45tiao-yue-h2u1r)发现其实不用全存，每次存到到边界需要几步就行了，人话就是：border 之内(包含 border)几步可达，结合代码更好理解

```cpp
class Solution {
public:
    int jump(vector<int> &nums) {
        if(nums.size()==1) return 0;

        int canGoMaxIndex=0,step=0;
        int broder=0;
        for(int i=0;i<nums.size();++i){
            canGoMaxIndex= max(canGoMaxIndex,i+nums[i]);
            if(i == broder){
                broder=canGoMaxIndex;
                step++;
                if(canGoMaxIndex>=nums.size()-1)  return step;
            }
        }
        return step;
    }
};
```

### 73 矩阵置0
- 第一种用集合存对应的行、列，最后集中置零
- 第二种用第一行，第一列做标记，（先记录第一行，第一列要不要置0），假如 `martix[i][j] = 0`，那么 `martix[i][0] = martix[0][j] = 0` （打上置零标记），最后集中处理

=== "set"

    ```cpp
    class Solution {
    public:
        set<int> rows, cols;

        void setZeroes(vector<vector<int>> &matrix) {
            for (int row = 0; row < matrix.size(); row++) {
                for (int col = 0; col < matrix[0].size(); col++)
                    if (matrix[row][col] == 0) {
                        rows.insert(row);
                        cols.insert(col);
                    }
            }
            for(int row:rows){
                for(int & item : matrix[row])
                    item=0;
            }
            for(int col:cols)
            {
                for(auto & i : matrix){
                    i[col]=0;
                }
            }
        }
    };
    ```

=== "flag"

    ```cpp
    class Solution {
    public:

        void setZeroes(vector<vector<int>> &matrix) {
            // 检查第 0 行 第 0 列
            int col0_flag = false, row0_flag = false;
            for (int item_row0: matrix[0])
                if (!item_row0) {
                    row0_flag = true;
                    break;
                }
            for (auto &i: matrix)
                if (!i[0]) {
                    col0_flag = true;
                    break;
                }
            
            // 检查中间
            for (int row = 1; row < matrix.size(); row++)
                for (int col = 1; col < matrix[0].size(); col++)
                    if (!matrix[row][col]) {
                        matrix[row][0] = matrix[0][col] = 0;
                    }
            // 修改中间
            for (int i = 1; i < matrix.size(); i++) {
                if (matrix[i][0] == 0)
                    for (int k = 1; k < matrix[0].size(); k++)
                        matrix[i][k] = 0;
            }
            for (int i = 1; i < matrix[0].size(); i++) {
                if (matrix[0][i] == 0)
                    for (int k = 1; k < matrix.size(); k++)
                        matrix[k][i] = 0;
            }

            // 处理第 0 行 第 0 列
            if (col0_flag)
                for (auto &i: matrix) 
                    i[0] = 0;
            if (row0_flag)
                for (int &i: matrix[0])
                    i = 0;
        }
    };
    ```

### 76 最小覆盖字串
有滑动窗口的提示，想起来是不是很难，但是写起来就容易超时。

思路：如果找到了一个覆盖，就更新答案，再把左指针移动到不能覆盖的地方

注意：每次移动一个字母的距离，不要把非答案字母用 while 全跳过，这样容易出问题

```cpp
class Solution {
public:
    unordered_map<char, int> cnt;
    unordered_map<char, int> should;

    string minWindow(string s, string t) {
        if (t.size() > s.size()) return "";

        pair<int, int> anw = {INT_MAX, INT_MAX};
        for (char x: t) should[x]++;

        int left = 0, right = 0;
        while (right < s.size()) {
            if (should[s[right]] > 0) cnt[s[right]]++;
            right++;
            while (check()) {
                if (right - left < anw.second) anw = {left, right - left};
                if (should[s[left]] > 0) cnt[s[left]]--;
                left++;
            }
        }

        cout << anw.first << ' ' << anw.second;
        if (anw.first == INT_MAX) return "";
        return s.substr(anw.first, anw.second);
    }

    bool check() {
        for (auto item: should) {
            if (cnt[item.first] < item.second) return false;
        }
        return true;
    }

};
```

优化

```cpp
class Solution {
public:
    unordered_map<char, int> letterNeed;

    string minWindow(string s, string t) {
        if (t.size() > s.size()) return "";

        pair<int, int> anw = {INT_MAX, INT_MAX};
        for (char x: t) letterNeed[x]++;

        int cnt = 0;
        for (int left = 0, right = 0; right < s.size(); right++) {
            letterNeed[s[right]]--;  // 上来就减不判断
            if (letterNeed[s[right]] >= 0) cnt++; // 非答案字母此时为 -1 
            while (cnt == t.size()) {
                if (right - left + 1 < anw.second) anw = {left, right - left + 1};
                letterNeed[s[left]]++;
                if (letterNeed[s[left]] > 0) cnt--; // 非答案字母此时为 0
                left++;
            }
        }

        if (anw.second == INT_MAX) return "";
        else return s.substr(anw.first, anw.second);
    }
};
```

### 78 子集
回溯没想出来，可以一个一个放，在上次的结果的每一项加一个新数。比如 `[1,2,3]` 的子集是在 `[1,2]` 的子集基础上每项都加 `3`

```cpp
class Solution {
public:
    vector<int> res;
    vector<vector<int>> anw;

    vector<vector<int>> subsets(vector<int> &nums) {
        res={};
        anw.push_back(res);
        for (int num: nums) {
            auto last=anw;
            for(auto item:last){
                item.push_back(num);
                anw.push_back(item);
            }
        }
        return anw;
    }
};
```

[回溯](https://leetcode.cn/problems/subsets/solutions/2566767/dai-ma-sui-xiang-lu-leetcode78zi-ji-by-c-yujc)

![回溯树](https://pic.leetcode.cn/1674874362-hBHNCS-image.png)

```cpp
class Solution {
public:
    vector<vector<int>> anw;
    vector<int> res;

    vector<vector<int>> subsets(vector<int> &nums) {
        dfs(nums, 0);
        return anw;
    }

    void dfs(vector<int> &nums, int begin_choose_index) {
        anw.push_back(res);

        if (begin_choose_index == nums.size()) {
            return;
        }
        for (int i = begin_choose_index; i < nums.size(); i++) {
            res.push_back(nums[i]);
            dfs(nums, i + 1);
            res.pop_back();
        }
    }
};
```

### 79 单词搜索
dfs

```cpp
class Solution {
public:
    string target;
    string now;
    vector<vector<bool>> used;
    int dx[4] = {0, 0, -1, 1};
    int dy[4] = {1, -1, 0, 0};

    bool exist(vector<vector<char>> &board, string word) {
        target = word;
        used = vector<vector<bool>>(board.size(),
                                    vector<bool>(board[0].size(), false));
        for (int i = 0; i < board.size(); i++)
            for (int j = 0; j < board[0].size(); j++) {
                bool anw = dfs(now, 0, i, j, board);
                if (anw)
                    return true;
            };
        return false;
    }

    bool dfs(string& now, int posi, int x, int y, vector<vector<char>> &board) {

        if (target[posi] != board[x][y])
            return false;

        now += board[x][y];
        used[x][y] = true;

        if (now == target)
            return true;

        for (int i = 0; i < 4; i++) {
            int nx = x + dx[i], ny = y + dy[i];
            if (nx >= 0 && nx < board.size() &&
                ny >= 0 && ny < board[0].size() &&
                !used[nx][ny])
                if (dfs(now, posi + 1, nx, ny, board))
                    return true;
        }

        now.pop_back();
        used[x][y] = false;

        return false;
    }
};
```

### 94 二叉树中序遍历
今天看到一个[好理解的非递归方法](https://leetcode.cn/problems/binary-tree-inorder-traversal/solutions/25220/yan-se-biao-ji-fa-yi-chong-tong-yong-qie-jian-ming)

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

### 99 恢复二叉搜索树
- 中序遍历的结果序列中，第一个逆序对 AB 的 A 是待换元素
- 如果有第二个逆序对，第二个逆序对 CD 的 D 是待换元素
- 如果没有第二个逆序对，就是 AB 互换
- 为什么这么说，可以写一个升序序列然后换其中的两个位置

[java 题解](https://leetcode.cn/problems/recover-binary-search-tree/solutions/271778/san-chong-jie-fa-xiang-xi-tu-jie-99-hui-fu-er-cha-)

```java
class Solution {
    //用两个变量x，y来记录需要交换的节点
    private TreeNode x = null;
    private TreeNode y = null;
    private TreeNode pre = null;
    public void recoverTree(TreeNode root) {
        dfs(root);
        //如果x和y都不为空，说明二叉搜索树出现错误的节点，将其交换
        if(x!=null && y!=null) {
            int tmp = x.val;
            x.val = y.val;
            y.val = tmp;
        }
    }
	
    //中序遍历二叉树，并比较上一个中序遍历节点(pre)和当前节点的值，如果pre的值大于当前节点值，则记录下这两个节点
    private void dfs(TreeNode node) {
        if(node==null) {
            return;
        }
        dfs(node.left);
        if(pre==null) {
            pre = node;
        }
        else {
            if(pre.val>node.val) {
                if(x==null) x = pre;
                y = node;
            }
            pre = node;
        }
        dfs(node.right);
    }
}
```

Mirros 遍历

```cpp
class Solution {
public:
    void recoverTree(TreeNode * root) {
        TreeNode * now = root;
        TreeNode * pre = nullptr;
        TreeNode * x = nullptr;
        TreeNode * y = nullptr;

        while (now) {
            TreeNode * mirros = now->left;
            if (mirros == nullptr) {
                if (pre && pre->val > now->val) {
                    if (x == nullptr) x = pre;
                    y = now;
                }
                pre = now;
                now = now->right;
                continue;
            }
            while (mirros->right && mirros->right != now) {
                mirros = mirros->right;
            }
            if (mirros->right == nullptr) {
                mirros->right = now;
                now = now->left;
            }
            else {
                if (pre && pre->val > now->val) {
                    if (x == nullptr) x = pre;
                    y = now;
                }
                mirros->right = nullptr;
                pre = now;
                now = now->right;
            }
        }
        if (x && y) swap(x->val, y->val);
    }
};
```

### 118 杨辉三角
难度不大，问题在于空间

```cpp
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ret(numRows);
        for (int i = 0; i < numRows; ++i) {
            ret[i].resize(i + 1);
            ret[i][0] = ret[i][i] = 1;
            for (int j = 1; j < i; ++j) {
                ret[i][j] = ret[i - 1][j] + ret[i - 1][j - 1];
            }
        }
        return ret;
    }
};
```

### 121 买卖股票1
相当于求 a[j]-a[i] 的最大值（j>i）

暴力就是双重循环，但是其实可以一个循环，利用无后效性，当我们扫到下标为 i 的元素时，只要有 a[0..i-1] 的最小值就好了，而这个最小值可以在扫到 i 之前就在维护

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int hold = INT_MAX; // 可以简化判断·
        int anw = 0;
        for (int price : prices) {
            if (hold < price)
                anw = max(anw, price - hold);
            hold = min(hold, price);
        }
        return anw;
    }
};
```

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

### 128 最长连续序列
#### 哈希表
一开始想的是哈希表记录出没出现，然后最大范围内遍历+ while 循环，超时了，后来改进成这样

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        int anw = 0;
        if (nums.empty())
            return 0;
        map<int, bool> appear;
        for (int x : nums)
            appear[x] = true;
        for (int x : nums) {
            int len = 1;
            while (appear[x - len])
                len++;
            anw = max(anw, len);
        }
        return anw;
    }
};
```

然后又超时了，看了答案后发现有很多冗余，比如说假如往回查(向小数)，4 就一定比 3 效果好，所以有 4 就不查 3

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        int anw = 1;
        if (nums.empty())
            return 0;
        unordered_map<int, bool> appear;
        for (int x : nums)
            appear[x] = true;
        for (int x : nums) {
            int len = 1;
            if (appear[x + 1])
                continue;
            while (appear[x - len])
                len++;
            anw = max(anw, len);
        }
        return anw;
    }
};
```

还可以往前查，有 3 就不查 4，但是这个表现不好，（我分析理论上应该差不多，实际可能是优化问题或者设计问题？）

gpt 给出的答案是

- 如果 nums 中升序多，往大了数快 `if (appear[x + 1]) continue;`
- 如果 nums 降序多，往小数快 `if (appear[x + 1]) continue;`
- 看起来有点合理

> The performance difference could arise from the order in which the numbers are checked for consecutiveness. It depends on the distribution of numbers in the nums vector. If the numbers are more likely to be consecutive in increasing order, the first approach might perform better. Conversely, if the numbers are more likely to be consecutive in decreasing order, the second approach might be more efficient.

??? "向大数"

    ```cpp
    class Solution {
    public:
        int longestConsecutive(vector<int>& nums) {
            int anw = 1;

            if (nums.empty())
                return 0;
            unordered_map<int, bool> appear;
            for (int x : nums)
                appear[x] = true;
            for (int x : nums) {

                int len = 1;
                if(appear[x-1]) continue;
                while (appear[x + len])
                    len++;
                anw = max(anw, len);
            }

            return anw;
        }
    };
    ```

#### 动态规划
第二种解法：动态规划，感觉这个方法不看答案是不容易想出来的。

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_map<int, int> appear;
        int anw = 0;

        for (int x : nums) {
            if (appear[x] == 0) {
                int total_len = 1;
                int left_len = appear[x - 1];
                int right_len = appear[x + 1];
                //eg: [1, 2, 3] 4 [5, 6]
                total_len =total_len+ left_len + right_len;
                appear[x - left_len] =appear[x]= appear[x + right_len] = total_len;
                anw = max(anw, total_len);
            }
        }
        return anw;
    }
};
```

#### 并查集
第三种解法：并查集思想

存下一个数，这样找到最远的数 right，right-x 就是答案，理论上存下一个和上一个差不多，但是存上一个就会超时

```cpp
class Solution {
public:
    unordered_map<int, int> map;

    int findmap(int x) {
        if (map.count(x)) {
            map[x] = findmap(map[x]);// 路径压缩
            return map[x];
        }
        else return x;
    }

    int longestConsecutive(vector<int> &nums) {
        int anw = 0;
        for (int x: nums) map[x] = x + 1;
        for (int x: nums) {
            int len = findmap(x) - x;
            anw = max(anw, len);
        }
        return anw;
    }
};

class Union {
    static const int N = 10;

    int root[N];
    int size[N];
public:
    Union() {
        for (int i = 0; i < N; i++) {
            root[i] = i;
            size[i] = 1;
        }
    }

    void unionab(int a, int b) {
        int root_a = get_root(a);
        int root_b = get_root(b);
        int atreeSize = size[root_a];
        int btreeSize = size[root_b];

        if (atreeSize < btreeSize) {
            root[root_a] = root_b;
            size[root_b] += atreeSize;
        }
        else {
            root[root_b] = root_a;
            size[root_a] += btreeSize;
        }
    }

    int get_root(int x) {
//        while (root[x] != x) {
//            root[x]=root[root[x]];// this can short path
//            x = root[x];
//        }// 两种写法
        if (root[x] != x)
            root[x] = get_root(root[x]);
        return x;
    }

    bool isConnected(int a, int b) {
        return get_root(a) == get_root(b);
    }

    void print() {
        for (int i = 0; i < N; ++i) {
            printf("root %d is %d\n", i, root[i]);
        }
    }
};
```

??? "TLE"

    ```cpp
    class Solution {
    public:
        unordered_map<int, int> map;

        int findmap(int x) {
            if (map.count(x)) {
                map[x] = findmap(map[x]);
                // 路径压缩
                return map[x];
            }
            else return x;
        }

        int longestConsecutive(vector<int> &nums) {
            int anw = 0;
            for (int x: nums) map[x] = x - 1;
            for (int x: nums) {
                int len = x - findmap(x);
                anw = max(anw, len);
            }
            return anw;
        }
    };

    ```

### 131 分割回文串
和 78 子集有点像，但是子集那个是路上全收集，这个部分收集

```cpp
class Solution {
public:
    vector<vector<string>> partition(string s) {
        dfs(0, s);
        return anw;
    }

    vector<vector<string>> anw;
    vector<string> res;

    void dfs(int begin_index, const string s) {
        if (begin_index == s.size()) {
            anw.push_back(res);
            return;
        }
        for (int i = begin_index; i < s.size(); i++) {
            string substr = s.substr(begin_index, i - begin_index + 1);
            if (isSim(substr))
                res.push_back(substr);
            else
                continue;
            dfs(i + 1, s);
            res.pop_back();
        }
    }

    bool isSim(const string s) {
        for (int i = 0; i < s.size() / 2; i++) {
            if (s[i] != s[s.size() - 1 - i])
                return false;
        }
        return true;
    }
};

```

### 138 随机链表复制
第一种，用哈希表

```cpp
class Solution {
public:
    unordered_map<Node *, Node *> mp;
    Node * copyRandomList(Node * head) {
        Node * dummy = new Node(0);
        Node * now = dummy;
        Node * h = head;
       
        while (h) {
            now->next = new Node(h->val);
            mp[h] = now->next;
            h = h->next;
            now = now->next;
        }

        h = head;
        now = dummy->next;
        while (h) {
            now->random = mp[h->random];
            now = now->next;
            h = h->next;
        }
        return dummy->next;
    }
};
```

第二种[题解](https://leetcode.cn/problems/copy-list-with-random-pointer/solutions/295083/liang-chong-shi-xian-tu-jie-138-fu-zhi-dai-sui-ji-)

用虚拟节点，防止原链表只有一个点。

```cpp
class Solution {
public:
    Node *copyRandomList(Node *head) {
        if (head == nullptr)
            return nullptr;
        auto h = head;
        auto now = head;
        while (h) {
            now = new Node(h->val);
            now->next = h->next;
            h->next = now;
            h = h->next->next;
        }

        h = head;
        while (h) {
            if (h->random)
                h->next->random = h->random->next;
            h = h->next->next;
        }

        h = head;
        Node *dummy = new Node(0);
        dummy->next=head;
        now = dummy;

        while (h) {
            now->next = h->next;
            h->next = h->next->next;
            now = now->next;
            h = h->next;
        }
        return dummy->next;
    }
};
```


### 146 LRU 缓存
双向链表加哈希表，靠近头的是最近使用的。每次操作先检查有没有，有的话就**先删除它与左右的联系再调到头部**（不然的话链表的指向就乱了）。理解起来难度不大，但是写起来太容易出错了。

=== "1 ed"

    ```cpp
    class MyNode {
    public:
        MyNode* prev;
        MyNode* next;
        int val;
        int key;

        MyNode(int _val) : val(_val) {}

        MyNode(int _key, int _val) : key(_key), val(_val) {}
    };

    class LRUCache {
    public:
        unordered_map<int, MyNode*> map;
        MyNode* dummy;
        int cap;
        LRUCache(int capacity) {
            cap = capacity;
            dummy = new MyNode(0);
            dummy->next = dummy;
            dummy->prev = dummy;
        }

        int get(int key) {
            if (map.find(key) == map.end())
                return -1;
            auto item = map[key];
            removeRelationship(item);
            setToFront(item);
            return item->val;
        }

        void put(int key, int value) {
            if (map.find(key) == map.end()) {
                auto item = new MyNode(key, value);
                map[key] = item;
                setToFront(item);

                if (map.size() > cap) {
                    auto todel = dummy->prev;
                    map.erase(todel->key);
                    removeRelationship(todel);
                    delete todel;
                }
            } 
            else {
                removeRelationship(map[key]);
                setToFront(map[key]);
                map[key]->val = value;
            }
        }

        void removeRelationship(MyNode* item) {
            item->prev->next = item->next;
            item->next->prev = item->prev;
        }

        void setToFront(MyNode* item) {
            item->next = dummy->next;
            item->prev = dummy;
            item->next->prev = item;
            item->prev->next = item;
        }
    };
    ```

=== "better"

    ```cpp
    class mynode {
    public:
        int key, value;
        mynode *left;
        mynode *right;

        mynode(int val) : value(val) {}

        mynode(int k, int v) : key(k), value(v) {}
    };

    class LRUCache {
    public:
        unordered_map<int, mynode *> map;
        mynode *dummy;
        int cap;

        LRUCache(int capacity) {
            cap = capacity;
            dummy = new mynode(0);
            dummy->left = dummy;
            dummy->right = dummy;
        }

        int get(int key) {
            auto item = getNode(key);
            return item ? item->value : -1;
        }

        void put(int key, int value) {
            auto item = getNode(key);
            if (item != nullptr) {
                item->value = value;
                return;
            }
            else {
                item = new mynode(key, value);
                map[key] = item;
                setToFront(item);

                if (map.size() > cap) {
                    auto delNode = dummy->left;
                    delRelationship(delNode);
                    map.erase(delNode->key);
                    delete delNode;
                }
            }

        }

        mynode *getNode(int key) {
            if (map.find(key) == map.end()) return nullptr;
            auto item = map[key];
            delRelationship(item);
            setToFront(item);
            return item;
        }

        void delRelationship(mynode *item) {
            item->left->right = item->right;
            item->right->left = item->left;
        }

        void setToFront(mynode *item) {
            item->left = dummy;
            item->right = dummy->right;
            item->left->right = item;
            item->right->left = item;
        }
    };
    ```

### 148 排序链表
归并排序

```cpp
class Solution {
public:
    ListNode *sortList(ListNode *head) {
        if (head == nullptr) return nullptr; // 特判原链表为空

        if (head->next == nullptr) return head;  // 归并终止

        ListNode *left = head;
        ListNode *leftPartEnd = getLeftPartEnd(head);
        ListNode *right = leftPartEnd->next;
        leftPartEnd->next = nullptr;

        left = sortList(left);
        right = sortList(right);
        return merge(left, right);

    }

    // 奇数个节点返回中间，偶数个节点返回左部分的最后一个
    ListNode *getLeftPartEnd(ListNode *head) {
        ListNode *slow = head;
        ListNode *fast = head->next;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }

    ListNode *merge(ListNode *left, ListNode *right) {
        ListNode dummy(0);
        ListNode *cur = &dummy;
        while (left && right) {
            if (left->val < right->val) {
                cur->next = left;
                left = left->next;
            }
            else {
                cur->next = right;
                right = right->next;
            }
            cur = cur->next;
        }
        if (left) cur->next = left;
        else cur->next = right;
        return dummy.next;
    }

};
```


第二种，每次从头排序的长度为 1, 2, 4 ...

```cpp
class Solution {
public:
    ListNode *sortList(ListNode *head) {

        int length = getLength(head);
        ListNode dummy(0);
        dummy.next = head;
        
        for (int i = 1; i < length; i*=2) {
            ListNode *sortedTail = &dummy;
            ListNode *cur = sortedTail->next;
            while (cur) {
                ListNode *left = cur;
                ListNode *right = cut(left, i);
                cur = cut(right, i);
                ListNode *newSortedHead = merge(left, right);
                sortedTail->next = newSortedHead;
                while (sortedTail->next) sortedTail = sortedTail->next;
            }
        }
        return dummy.next;
    }

    ListNode *merge(ListNode *left, ListNode *right) {
        ListNode dummy(0);
        ListNode *cur = &dummy;
        while (left && right) {
            if (left->val < right->val) {
                cur->next = left;
                left = left->next;
            }
            else {
                cur->next = right;
                right = right->next;
            }
            cur = cur->next;
        }
        if (left) cur->next = left;
        else cur->next = right;
        return dummy.next;
    }

    ListNode *cut(ListNode *head, int cnt) {
        for (int i = 1; i < cnt && head; i++) {
            head = head->next;
        }
        if (!head) return nullptr;

        ListNode *ne = head->next;
        head->next = nullptr;
        return ne;
    }

    int getLength(ListNode *head) {
        int cnt = 0;
        while (head) {
            cnt++;
            head = head->next;
        }
        return cnt;
    }
};
```

### 155 最小栈
用一个辅助栈存最小值，push 如果 `val <= minst.top` push 进，不然跳过，假如说是 5， 3， 4 这个例子，只要 3 不从真实栈出来，无论后面 4 怎么进出真实栈都不改变最小值。但如果又来一个 3 就要进辅助栈，是为了弹出后来的 3 时，辅助栈最小值还是 3（之前先来的那个）

另外为什么不是所有数字的单调栈？还是 5， 3， 4 这个例子，当 4 从真实栈弹出的时候是不能操作到单调栈的 `|| 5 4 3 (`

### 189 轮转数组
线性代数：在想我的事情？😋

$$(a^Tb^T)^T=ba$$

矩阵的转置或者求逆有上边的性质，可以发现字符串的逆序也符合这个性质。逆序映射转置，单射函数，先分别逆序，再全部逆序即可。

细节处理：轮转 k 个相当于把前 size-k 个数放到最后，当 k=size 相当于前 0 个放到最后，就是原样不动，所以 `if k > nums.size k = k % nums.size`

=== "24ms"

    ```cpp
    class Solution {
    public:
        void test() {
            vector<int> te = {1, 2, 3, 4, 5, 6, 7};
            rotate(te, 3);
        }
        void rotate(vector<int>& nums, int k) {
            k %= nums.size();
            reverse(nums.begin(), nums.begin() + nums.size() - k);
            reverse(nums.begin() + nums.size() - k, nums.end());
            reverse(nums.begin(), nums.end());
            return;
        }
    };
    ```

=== "7ms"

    ```cpp
    class Solution {
    public:
        void test() {
            vector<int> te = {1, 2, 3, 4, 5, 6, 7};
            rotate(te, 3);
        }


        void rotate(vector<int> &nums, int k) {

            if (k <= nums.size()) {
                reverse(nums.begin(), nums.begin() + nums.size() - k);
                reverse(nums.begin() + nums.size() - k, nums.end());
                reverse(nums.begin(), nums.end());
                return;
            }
            else {
                rotate(nums, k - nums.size());
            }
        }
    };
    ```


### 198 打家劫舍
开始想的不对，不用标记上一个偷没偷，直接取就行了

=== "空间On"

    ```cpp
    class Solution {
    public:
        int rob(vector<int> &nums) {
            if(nums.empty()) return 0;
            if(nums.size()==1) return nums[0];
            vector<int>dp(nums.size(),0);
            dp[0]=nums[0];
            dp[1]=max(nums[1],nums[0]);

            for (int i = 2; i <nums.size() ; ++i) {
                dp[i]= max(dp[i-1],dp[i-2]+nums[i]);
            }
            return dp[nums.size()-1];
        }
    };
    ```

=== "O1"

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        int size = nums.size();
        if (size == 1) {
            return nums[0];
        }
        int first = nums[0], second = max(nums[0], nums[1]);
        for (int i = 2; i < size; i++) {
            int temp = second;
            second = max(first + nums[i], second);
            first = temp;
        }
        return second;
    }
};
```

### 199 二叉树的右视图
观察到答案是每层的最后一个

```cpp
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {

        vector<int> anw;
        if (root == nullptr)
            return anw;
        queue<TreeNode*> qu;
        qu.push(root);
        while (!qu.empty()) {
            int k = qu.size();
            for (int i = 0; i < k; i++) {
                TreeNode* item = qu.front();
                qu.pop();
                if (i == k - 1)
                    anw.push_back(item->val);
                if (item->left)
                    qu.push(item->left);
                if (item->right)
                    qu.push(item->right);
            }
        }
        return anw;
    }
};
```

dfs：根右左，如果答案个数和层数（从 0 开始）相同，放入

```cpp
class Solution {
public:
    vector<int> anw;

    vector<int> rightSideView(TreeNode *root) {

        if (root == nullptr) return anw;
        dfs(root, 0);
        return anw;
    }

    void dfs(TreeNode *root, int depth) {
        if (root == nullptr) return;
        if (depth == anw.size()) anw.push_back(root->val);

        dfs(root->right, depth + 1);
        dfs(root->left, depth + 1);
    }
};
```

### 213 打家劫舍2
看成是 0-n-1 和 1-n 两部分

```cpp

class Solution {
public:
    int rob(vector<int> &nums) {
        if (nums.size() == 1) return nums[0];
        if (nums.size() == 2) return max(nums[1], nums[0]);

        int anw0 = choose(nums, 0, nums.size() - 2);
        int anw1 = choose(nums, 1, nums.size() - 1);
      //  cout <<anw0<<' ' << anw1;
        return max(anw0, anw1);
    }

    int choose(vector<int> &nums, int begin, int end) {
        vector<int> anw(end - begin + 1);
        anw[0] = nums[begin];
        anw[1] = max(nums[begin], nums[begin + 1]);
        if (begin == 1)
            for (int i = 2; i < anw.size(); i++)
                anw[i] = max(anw[i - 1], anw[i - 2] + nums[i + 1]);
        else {
            for (int i = 2; i < anw.size(); i++)
                anw[i] = max(anw[i - 1], anw[i - 2] + nums[i]);
        }
        return anw[anw.size() - 1];
    }
};
```

### 215 数组中的第K个最大元素
快速选择，经典题目。

普通快排：每次快排后 `[lo,j]` 的元素是 `<=x` 的，`[j+1,hi]` 的元素是 `>=x` 的，当 `lo==hi` 就是答案。要找第 k 大，直接数组弄成降序，寻找下标为 k-1 的元素

```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        return quick(nums, 0, nums.size() - 1, k - 1);
    }

    int quick(vector<int>& nums, int lo, int hi, int k) {
        if (lo >= hi)
            return nums[k];
        int i = lo - 1, j = hi + 1;
        int x = nums[lo + hi >> 1];
        while (i < j) {
            do i++; while (nums[i] > x);
            do j--; while (nums[j] < x);
            if (i < j)
                swap(nums[i], nums[j]);
        }
        if (k <= j)
            return quick(nums, lo, j, k);
        else
            return quick(nums, j + 1, hi, k);
    }
};
```

### 225 两个队列实现栈
每次放的时候先放到辅助队列，把主队列的内容全导入辅助，在把辅助队列导入主队列

或者放的时候先放到主队列，出的时候先把前 n-1 个放到辅助队列，弹出答案，再把辅助队列内容挪回主队列

```cpp
class MyStack {
public:
    queue<int> que;
    queue<int> aux;

    MyStack() {

    }

    void push(int x) {
        aux.push(x);
        while (!que.empty()) {
            aux.push(que.front());
            que.pop();
        }
        swap(que, aux);
    }
    
    int pop() {
        int r = que.front();
        que.pop();
        return r;
    }
    
    int top() {
        int r = que.front();
        return r;
    }
    
    bool empty() {
        return que.empty();
    }
};


```

### 229 多数元素2
- 如果已经出现了，次数加一
- 如果没出现
    - 桶还能放，就放到桶里
    - 没有多余的桶，所有的次数减一

可能有出现在数组靠后的元素，会占到桶里，所以重新统计一次（桶的个数多于 1）,或者抵消了一部分正确数字次数 `eg:[2, 2, 1, 3]`

桶的个数：假如说要找超过 `n/k` 个的元素，桶的个数就是 `k-1` 。假如取 `x` 个， `x*(n/k) <= n  ---> x <= k` ，又因为超过 `n/k` ，所以取 `k-1`

```cpp
class Solution {
public:
    //first 存数字，second 存出现次数
    pair<int, int> bucket[2];

    void write(int x) { 
        // item 取引用，因为要修改里边的元素,不然的话里边的值不变
        for (auto &item: bucket)
            if (item.first == x) {
                item.second++;
                return;
            }
        for (auto &item: bucket)
            if (item.second == 0) {
                item.first = x;
                item.second = 1;
                return;
            }
        for (auto &item: bucket)
            item.second--;
       
    }

    vector<int> majorityElement(vector<int> &nums) {
        bucket[0].first = bucket[1].first = INT_MAX;
        for (auto x: nums) {
            write(x);
        }

        vector<int> res;
        bucket[0].second = bucket[1].second = 0;

        for (auto x: nums) {
            if (x == bucket[0].first)
                bucket[0].second++;
            else if (x == bucket[1].first)
                bucket[1].second++;
        }
        if (bucket[0].second > nums.size() / 3)
            res.push_back(bucket[0].first);
        if (bucket[1].second > nums.size() / 3)
            res.push_back(bucket[1].first);
        return res;
    }
};
```



### 230 二叉搜索树第k个元素
中序遍历时统计个数

```cpp
class Solution {
public:
    int kthSmallest(TreeNode *root, int k) {

        this->k = k;
        dfs(root);
        return anw;
    }

    int k, anw, cnt;

    void dfs(TreeNode *root) {
        if (root == nullptr) return;
        dfs(root->left);
        cnt++;
        if (cnt == k) {
            anw = root->val;
            return;
        }
        dfs(root->right);
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

### 236 二叉树的最近公共祖先
只感觉出是后序遍历（当时红皮书图论看有没有环），但是在祖先是本身和上边节点卡了

```cpp
class Solution {
public:
    TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *p, TreeNode *q) {
        // 找到就返回一个，所以最上层
        if (root == nullptr || root == q | root == p) return root;
        TreeNode *left = lowestCommonAncestor(root->left, p, q);
        TreeNode *right = lowestCommonAncestor(root->right, p, q);
        if (left && right) return root;
        else left ? left : right;
    }
};
```

### 238 除自身以外数组的乘积
前缀和思路，这个是前缀乘和后缀乘。

```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int> &nums) {
        int sz = nums.size();
        vector<int> anw(sz, 1);
        int right_mul=1;
        for (int i = 1; i < sz; ++i) {
            anw[i]=anw[i-1]*nums[i-1];
        }
        for(int i=sz-2;i>=0;--i){
            right_mul*=nums[i+1];
            anw[i]=anw[i]*right_mul;
        }
        return anw;
    }
};
```



### 239 滑动窗口最大值
最开始想到的是窗口新进的值更大，那么窗口里所有比它小的都不是答案了，看提示发现是双端队列存候选答案，不是答案不用存。这也是单调队列（可以相等元素）的一个应用

```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int> &nums, int k) {

        deque<int> deque;
        vector<int> anw;
        for (int right = 0, left = 0; right < nums.size(); right++) {
            while (!deque.empty() && nums[right] > deque.back()) {
                deque.pop_back();
            }
            deque.push_back(nums[right]);

            if (right - left + 1 > k) {
                if (nums[left] == deque.front()) deque.pop_front();
                left++;
            }
            if (right-left+1 == k)
                anw.push_back(deque.front());
        }
        return anw;
    }
};
```

### 240 搜索二维矩阵
从右上角往左边和下边看是二叉树

```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {

        int row=0,col=matrix[0].size()-1;
        while (row>=0&&row<matrix.size()&&col>=0&& col<matrix[0].size()){
            if(matrix[row][col]==target) return true;
            else if(matrix[row][col]<target) row++;
            else col--;
        }
        return false;
    }
};
```

### 283 移动0
比较好的做法是双指针，一个指向新的开始，一个找应该出现的下一位

```cpp
class Solution {
public:
    void moveZeroes(vector<int> &nums) {
        int choose = 0;
        for (int new_begin = 0; new_begin < nums.size(); ++new_begin) {
            while (choose < nums.size() && !nums[choose]) choose++;
            if (choose < nums.size())
                nums[new_begin] = nums[choose++];
            else {
                while (new_begin<nums.size())
                    nums[new_begin++]=0;
                break;
            }
        }
    }
};
```

不太好的做法是互换，遇到一个 0，就找下一个非 0，进行互换，没有非零就结束，但是需要检查 `[1, 0]` 这种

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {

        int next = 0;
        while (next < nums.size() && nums[next] != 0)
            next++;     // find first 0
        while (next < nums.size() && nums[next] == 0)
            next++;     // find first positive num after first 0

        for (int new_start = 0; new_start < nums.size(); ++new_start) {
            if (nums[new_start] == 0) {
                while (next < nums.size() && nums[next] == 0)
                    next++;
                if (next >= nums.size())
                    break;
                else
                    swap(nums[new_start], nums[next]);
            }
        }
    }
};
```


### 287 寻找重复数

以[1,3,4,2,2]为例，如果有相同数字，相当于会存在一个环

**核心**：
下标和内容一起做指向

| 下标 | 0   | 1   | 3   | 2   | 4       |
| ---- | --- | --- | --- | --- | ------- |
| 内容 | 1   | 3   | 2   | 4   | 2(成环) |
| 节点 | 1   | 3   | 2   | 4   | 2       |

然后就和[环形链表2](https://leetcode.cn/problems/linked-list-cycle-ii/description/)一个做法，判环找入口

```C++
class Solution {
public:
    vector<int> num;

    int next(int x) { return num[x]; }

    int findDuplicate(vector<int> &nums) {
        num = nums;
        int slow = 0;
        int fast = 0;
        do {
            slow = next(slow);
            fast = next(next(fast));
        } while (slow != fast);

        fast = 0;
        while (fast != slow) {
            fast = next(fast);
            slow = next(slow);
        }
        return fast;
    }
};
```


### 347 前 K 个高频元素
哈希表记录出现次数，用最大堆挑出答案

```cpp
class Solution {
public:
    unordered_map<int, int> map;
    priority_queue<pair<int, int>, vector<pair<int, int>>> pq;

    vector<int> topKFrequent(vector<int> &nums, int k) {
        vector<int> anw;
        for (int x: nums) map[x]++;
        for (auto item: map)pq.emplace(item.second, item.first);
        for (int i = 0; i < k; ++i) {
            auto item = pq.top();
            pq.pop();
            anw.push_back(item.second);
        }
        return anw;
    }
};
```

### 394 字符串解码
看起来挺简单的，做起来就不是一回事了。直接背下来，另外不要一次处理太多，比如看到一个数字不要再开 while 处理，不然的话在下标上就会很难处理。

```cpp
class Solution {
public:
    string decodeString(string s) {

        // 存的是接下来的串应该重复多少次，和上一次处理结果
        stack<pair<int, string>> st;
        int num = 0;
        string current_process;
        for (char i: s) {
            if (i >= '0' && i <= '9') {
                num *= 10;
                num += (i - '0');
            }
            else if (i == '[') {
                st.emplace(num, current_process);
                num = 0;
                current_process.clear();
            }
            else if (i == ']') {
                int n = st.top().first;
                // n指示的是current的循环次数，不是last_result的
                string last_result = st.top().second;
                st.pop();
                for (int k = 0; k < n; k++)
                    last_result.append(current_process);
                
                current_process = last_result;
            }
            else {
                current_process += i;
            }
        }
        return current_process;
    }
};
```


### 402 移掉K位数字
假如只要删 1 位 `4321,2341,4231` 很容就看出答案应该删除 4

周姓室友直觉很敏锐啊，如果当前处理的那一位前边有比它大的，大的就应该删除，很自然的想到看前边的状态就用栈。这个题就是单调栈（或者说是从前到后选的时候尽量选一些小的数字，比如 `2341` 选 4 不选 1）

还有一些细节

- 前导零，最开始做法是把 0 也压栈，最后一起处理，另一种是在循环时就处理（什么时候不能入栈呢，就是栈为空且当前处理为 0，反过来就是什么时候能入栈）
- 空间，开始是用 `anw= x+ anw` 然后 MLE 了，用 `anw += x` 加 `reverse` 就不会
- 可能没删除够，就弹栈

=== "primary"

    ```cpp
    class Solution {
    public:
        string removeKdigits(string num, int k) {

            if (k >= num.size()) return "0";

            stack<char> st;
            int removedCnt = 0;
            for (char x: num) {
                while (!st.empty() && st.top() > x && removedCnt < k) {
                    st.pop();
                    removedCnt++;
                }
                st.push(x);
            }

            while (removedCnt < k) { // 防止没删除够
                st.pop();
                removedCnt++;
            }

            string anw;
            while (!st.empty()) {
                anw += st.top();
                st.pop();
            }

            std::reverse(anw.begin(), anw.end());

            int begin = 0; // 删除前导零
            while (begin < anw.size() && anw[begin] == '0') begin++;
            anw = anw.substr(begin);

            if (anw.empty() || anw[0] == '0') return "0";
            return anw;
        }
    };
    ```

=== "better"

    ```cpp
    class Solution {
    public:
        string removeKdigits(string num, int k) {

            if (k >= num.size()) return "0";

            stack<char> st;
            int removedCnt = 0;
            for (char x: num) {
                while (!st.empty() && st.top() > x && removedCnt < k) {
                    st.pop();
                    removedCnt++;
                }
                if (!st.empty() || x != '0')
                    st.push(x);
            }

            while (removedCnt < k&&!st.empty()) {
                st.pop();
                removedCnt++;
            }

            string anw;
            while (!st.empty()) {
                anw += st.top();
                st.pop();
            }
            std::reverse(anw.begin(), anw.end());

            if (anw.empty()) return "0";
            return anw;
        }
    };
    ```

### 437 路径总和

```cpp
class Solution {
public:
    unordered_map<long long, int> map;
    int anw = 0;

    int pathSum(TreeNode *root, int targetSum) {
        map[0] = 1;
        dfs(root, 0, targetSum);
        return anw;
    }

    void dfs(TreeNode *root, long long sum, int targetSum) {
        if (root == nullptr) return;
        
        sum += root->val;
        if (map.count(sum - targetSum))
            anw += map[sum - targetSum];
            
        map[sum]++;
        dfs(root->left, sum, targetSum);
        dfs(root->right, sum, targetSum);
        map[sum]--;

    }
};
```

### 438 找到字符串中所有字母异位词
滑动窗口

```cpp
class Solution {
public:
    map<char, int> cnt;
    map<char, int> should;

    bool check(const string& p) {
        for (auto item : should) {
            if (cnt[item.first] != item.second)
                return false;
        }
        return true;
    }

    vector<int> findAnagrams(string s, string p) {
        for (char x : p)
            should[x]++;

        vector<int> anw;
        int left, right;
        for (left = 0, right = 0; right < s.size(); right++) {
            cnt[s[right]]++;
            while (right - left + 1 > p.size()) { // 长度超了就缩小
                cnt[s[left]]--;
                left++;
            }
            if (right - left + 1 == p.size() && check(p)) { // 找到了答案
                anw.push_back(left);
                // 下边可有可无
                // cnt[s[left]]--;  
                // left++;
            }
        }
        return anw;
    }
};
```

### 543 二叉树的直径
后序遍历找到左子树的最大深度，右子树的最大深度，加起来就是当前节点为根的直径，有可能不经过根节点，所以用全局变量

```cpp
class Solution {
public:
    int anw = 0;
    int diameterOfBinaryTree(TreeNode* root) {
        if (root == nullptr) return 0;
        dfs(root);
        return anw;
    }

    int dfs(TreeNode* root) {
        if (root == nullptr) return 0;
        int left = dfs(root->left);
        int right = dfs(root->right);
        anw = max(anw, left + right);
        return max(left, right) + 1;
    }
};
```

### 560 和为 K 的子数组
开始以为是滑动窗口，但是带负数，做不出来，提示有前缀和，之后暴力找的

```cpp
class Solution {
public:
    int subarraySum(vector<int> &nums, int k) {

        vector<int> prefix(nums.size() , 0);
        prefix[0] = nums[0];
        for (int i = 1; i < prefix.size(); i++) {
            prefix[i] = prefix[i - 1] + nums[i];
        }

       int anw=0;
        for(int i=0;i<prefix.size();i++){
            if(prefix[i]==k) anw++;
            for(int r=i+1;r<prefix.size();r++){
                if(prefix[r]-prefix[i]==k) anw++;
            }
        }
        return anw;
    }
};
```

看了题解，前缀和加哈希表，每次相当于找 `prefix[j]-prefix[i]=k, j>i` 进行移项后 `prefix[j]-k=prefix[i], j>i` 可以看成两数之和那道题，非常的巧妙

为了解决 `prefix[j] = k` 的情况 ` eg: [1, 0] k = 1`，有两种方法

1. 上来就记录 `mp[0] = 1`
2. 循环内特判

=== "未优化"

    ```cpp
    class Solution {
    public:
        int subarraySum(vector<int> &nums, int k) {

            vector<int> prefix(nums.size(), 0);
            prefix[0] = nums[0];
            for (int i = 1; i < prefix.size(); i++) {
                prefix[i] = prefix[i - 1] + nums[i];
            }
            unordered_map<int, int> mp;
            mp[0] = 1;
            int anw = 0;
            for (int i = 0; i < prefix.size(); i++) {
                if (mp.find(prefix[i] - k) != mp.end())
                    anw += mp[prefix[i] - k];
                mp[prefix[i]]++;
            }
            return anw;
        }
    };
    ```

=== "优化1"

    ```cpp
    class Solution {
    public:
        int subarraySum(vector<int> &nums, int k) {
            int anw=0,sum=0;
            unordered_map<int,int>mp;
            mp[0]=1;
            for(int num:nums){
                sum+=num;
                if(mp.find(sum-k)!=mp.end())
                    anw+=mp[sum-k];
                mp[sum]++;
            }
            return anw;
        }
    };
    ```

=== "优化2"

    ```cpp
    class Solution {
    public:
        int subarraySum(vector<int> &nums, int k) {
            int anw=0,sum=0;
            unordered_map<int,int>mp;
        //  mp[0]=1;  // 不进行 mp[0]=1 就特判
            for(int num:nums){
                sum+=num;
                if(sum==k) anw++;
                if(mp.find(sum-k)!=mp.end()) // 这行没有也行，因为没有的项结果为0
                    anw+=mp[sum-k];
                mp[sum]++;
            }
            return anw;
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


### 904 水果成篮
就是找一段区间尽可能长，区间内只有两种元素

用滑动窗口

```cpp
class Solution {
public:
    int totalFruit(vector<int> &fruits) {

        int anw = 0;
        unordered_map<int, int> pick;

        for (int left = 0, right = 0; right < fruits.size(); right++) {
            pick[fruits[right]]++;

            while (pick.size() > 2) {
                pick[fruits[left]]--;
                if (pick[fruits[left]] == 0) pick.erase(fruits[left]);
                left++;
            }

            anw = max(anw, right - left + 1);
        }
        return anw;
    }
};
```

不太好的想法：最开始用两个桶，有空桶就放到里面，没有空桶且当前值不在桶里，更新一个桶，问题在于哪个：最近上一次访问的水果种类的桶是不能更新的

这个不好在于更新 left 是反复往左的，比较慢

```cpp
class Solution {
public:
    int totalFruit(vector<int> &fruits) {

        int anw = 0;
        int res = 0;
        int pick[2] = {-1, -1};
        for (int left = 0, right = 0; right < fruits.size(); right++) {
            if (pick[0] == -1) {
                pick[0] = fruits[right];
                res++;
            }
            else if (pick[1] == -1) {
                pick[1] = fruits[right];
                res++;
            }
            else if (pick[0] == fruits[right] || pick[1] == fruits[right]) res++;
            else {
                anw = max(anw, res);
                left = right - 1;
                while (left > 0 && fruits[left] == fruits[left - 1]) left--;
                res = right - left + 1;
                if (pick[0] != fruits[right-1]) pick[0] = fruits[right];
                else pick[1] = fruits[right];
            }
        }
        anw= max(anw,res);
        return anw;
    }
};
```

### 1475 商品折扣后最终价格
这个题和 739 是一个类型，假设 `[4,8,3,7]`，可以用 3 更新前边的 4，8，也就是当前的值比前边的小，就出栈之前内容并更新。

完全可以先复制一份一模一样的，更新的话能更新好之前的。不能更新的也是本身。要不然就是最后再全部出栈

```cpp
class Solution {
public:
    vector<int> finalPrices(vector<int> &prices) {
        vector<int> anw=prices; // 复制构造函数
        stack<int> st;
        for (int i = 0; i < prices.size(); i++) {
            while (!st.empty() && prices[i] <= prices[st.top()]) {
                int pre_anw_index = st.top();
                st.pop();
                anw[pre_anw_index] = prices[pre_anw_index] - prices[i];
            }
            st.push(i);
        }
      
        return anw;
    }
};
```

??? "全部出栈"

    ```cpp
    class Solution {
    public:
        vector<int> finalPrices(vector<int> &prices) {
            vector<int> anw(prices.size(), 0);
            stack<int> st;
            for (int i = 0; i < prices.size(); i++) {
                while (!st.empty() && prices[i] <= prices[st.top()]) {
                    int pre_anw_index = st.top();
                    st.pop();
                    anw[pre_anw_index] = prices[pre_anw_index] - prices[i];
                }
                st.push(i);
            }
            while (!st.empty()) {
                int pre_anw_index = st.top();
                st.pop();
                anw[pre_anw_index] = prices[pre_anw_index];
            }
            return anw;
        }
    };
    ```


### 763 划分字母区间
最开始没什么想法，然后仔细读题，发现相同字母都在一个区间，然后连了一下同一个字母的最前和最后，发现可以看成合并区间那道题

```cpp
class Solution {
public:

    unordered_map<char, pair<int, int>> dic;

    bool canMerge(pair<int, int> a, pair<int, int> b) {
        if (a.first > b.second || a.second < b.first)
            return false;
        else return true;
    }

    vector<int> partitionLabels(string s) {
        int sz = s.size();
        for (int i = 0; i < sz; ++i) {
            if (dic.count(s[i])) {
                dic[s[i]].first = min(dic[s[i]].first, i);
                dic[s[i]].second = max(dic[s[i]].second, i);
            }
            else {
                dic[s[i]] = {i, i};
            }
        }

        vector<int> anw;
        pair<int, int> broder = {0, 0};

        for (char x: s) {
            if (canMerge(dic[x], broder)) {
                broder.first = min(broder.first, dic[x].first);
                broder.second = max(broder.second, dic[x].second);
            }
            else {
                anw.push_back(broder.second - broder.first + 1);
                broder = dic[x];
            }
        }
        // don't forget push the last border
        anw.push_back(broder.second-broder.first+1);
        return anw;
    }
};
```

过了之后发现时间有点落后，看题解，说的是如果 **i == 当前字母出现的最远位置**，说明找到一个区间，![题解图片](https://pic.leetcode.cn/1683277847-xxagyB-image.png)

```cpp
class Solution {
public:
    vector<int> partitionLabels(string s) {
        unordered_map<char, int> dic;
        int sz = s.size();
        for (int i = 0; i < sz; ++i) {
            dic[s[i]] = max(dic[s[i]], i);
        }

        int left = 0, right = 0;
        vector<int> anw;
        for (int i = 0; i < sz; ++i) {
            right = max(right, dic[s[i]]);
            if (i == right) {
                anw.push_back(right - left + 1);
                left = i + 1;
            }
        }
        return anw;
    }
};
```

### 1658 将 x 减到 0 的最小操作数
反转问题，相当于找一段长度尽可能大的连续子数组，`子数组之和等于整个数组和 - x`

```cpp
class Solution {
public:
    int minOperations(vector<int> &nums, int x) {

        int anw = nums.size()+1; // 防止答案是整个数组

        int total_sum = 0;
        for (const int &num: nums) total_sum += num;
        int target = total_sum - x;
        if(target<0) return -1;

        int window_sum = 0;
        for (int left = 0, right = 0; right < nums.size(); right++) {
            window_sum += nums[right];

            if (window_sum < target) continue;
            while (window_sum > target) {
                window_sum -= nums[left];
                left++;
            }
            if (window_sum == target) {
                anw = min(anw, int(nums.size() - (right - left + 1)));
            }
        }
        if (anw == nums.size()+1) return -1;
        else return anw;
    }
};
```

### 1953 工作最大周数
好比插空，最大值 max_element,剩下的为 rest。

- 如果 `rest <= max_element - 1` 合法的有 `2*rest+1`
- 如果 `rest > max_element - 1` 说明都能完成，因为假如先从大到小排序，从前到后插空都能插进去。`eg: [5, 4, 3]`

```cpp
class Solution {
public:
    long long numberOfWeeks(vector<int> &milestones) {
        long long max_ele= *std::max_element(milestones.begin(), milestones.end());
        long long rest=0;
        for(int x:milestones) rest+=x;
        rest-=max_ele;
        if(rest<=max_ele-1) return 2 * rest + 1;
        else return max_ele + rest;
    }
};
```

### 2335 装满杯子
正确方法是每次取剩余水最多的两个，直到只剩一杯水

```cpp
class Solution {
public:
    int fillCups(vector<int>& amount) {
        sort(amount.begin(),amount.end());
        if(amount[1]==0) return amount[2];
        amount[1]--;amount[2]--;
        return 1+fillCups(amount);
    }
};
```

直接算：从小到大排序为 a, b, c

- `c >= a + b` 结果为 c
- `c < a + b` ，多出来的为 deta
    - 如果 deta 为偶数，经过 `deta/2` 次后， `a'+b' = c`，结果为 `deta/2 + c`。
    - 如果 deta 为奇数，操作 `(deta-1)/2` 次后，`a'+ b'= c + 1` 结果为 `(deta-1)/2 + c + 1`

```cpp
class Solution {
public:
    int fillCups(vector<int>& amount) {
        sort(amount.begin(), amount.end());
        int a = amount[0], b = amount[1], c = amount[2];
        if (c >= a + b)
            return c;
        else
            return (a + b - c + 1) / 2 + c;
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




### 最短无序连续子数组
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


### 最长上升子序列
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

### 数组中的逆序对

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

### 编辑距离
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

### 75 颜色分类

=== "dutchFlag"

    ```cpp
    class Solution {
    public:
        void sortColors(vector<int> &nums) {
            // num 0 is red, 1 is white, 2 is blue
            int red = 0, white = 0, blue = nums.size() - 1;
            while (white <= blue) {
                if (nums[white] == 0) {
                    swap(nums[white], nums[red]);
                    red++;
                    white++; // white red 都指向0，两者互换后都前进
                }
                else if (nums[white] == 2) {
                    swap(nums[white], nums[blue]);
                    blue--;
                }
                else white++;
            }
        }
    };
    ```

=== "刷油漆"

    ```cpp
    class Solution {
    public:
    void sortColors(vector<int> &nums) {
        int l0 = 0, l1 = 0;
        for (int i = 0; i < nums.size(); i++) {
            int value = nums[i];
            nums[i] = 2;
            if (value <= 1) nums[l1++] = 1;
            if (value == 0) nums[l0++] = 0;
        }
    }
    };
    ```

The Dutch national flag. [wikipedia](https://en.wikipedia.org/wiki/Dutch_national_flag_problem)

sort an array of some 0,1,2 in O(n) 

- [0, i-1] < midElement
- [i, j-1] = midElement
- [j, k] unsorted
- [k+1, end] >midElement

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

### lakes
[链接](https://codeforces.com/contest/1829/problem/E)

这个题在于剪枝，有的不用再dfs了，不然超时。假如（1，1）和（1，2）联通，dfs（1，1）和dfs（1，2）是一个结果。

```cpp
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

### Hits Different
[链接](https://codeforces.com/contest/1829/problem/G)

[前缀和动画讲解](https://usaco.guide/silver/more-prefix-sums?lang=cpp#2d-prefix-sums)

非常巧妙啊，转成前缀和,详情可以见相应英文题解

```cpp
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

### Distinct Split
[链接](https://codeforces.com/contest/1791/problem/D)

1. 一次遍历统计出所有字母的出现次数
2. 从前往后开始算，给pre分一个字母，就在该字母出现总数-1
3. 统计所有字母，进行加和；

巧妙在相当于并行处理2个字符串，想不出来😥 

