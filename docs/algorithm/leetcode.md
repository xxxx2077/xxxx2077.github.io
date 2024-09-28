# leetcode

## 题单

### 华为推荐题型List

⚠️机考题型参考(如有竞赛经历找群主确认是否可以免考)：
递归：leetcode70、112、509
分治：leetcode23、169、240
单调栈：leetcode84、85、739、503
并查集：leetcode547、200、684
滑动窗口：leetcode209、3、1004、1208
前缀和：leetcode724、560、437、1248
差分：leetcode1094、121、122
拓扑排序：leetcode210
字符串：leetcode5、20、43、93
二分查找：leetcode33、34
BFS：leetcode127、139、130、529、815
DFS&回溯：leetcode934、685、1102、531、533、113、332、337
动态规划：leetcode213、123、62、63、361、1230
贪心：leetcode55、435、621、452
字典树：leetcode820、208、648

## 1 两数之和

### 暴力版

```C++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                if (nums[i] + nums[j] == target) {
                    return {i, j};
                }
            }
        }
        return {};
    }
};
```



### 优化版

```C++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        // 使用哈希表，因为想要将查询target - nums[i]的时间复杂度缩为O(1)
        unordered_map<int,int> hashMap;
        vector<int> ans;
        // 遍历一遍数组，使用hashMap记录nums[i]之前的元素值和下标
        for(int i = 0; i < nums.size(); i++){
            // 如果hashMap存在target - nums[i],直接返回
            auto it = hashMap.find(target - nums[i]);
            if(it != hashMap.end()){
                ans.push_back(it->second);
                ans.push_back(i);
                break;
            }
            // 如果不存在，更新hashMap
            hashMap[nums[i]] = i;
        }
        return ans;
    }
};
```

## 3 无重复字符的最长子串

```C++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int n = s.size();
        int start = 0;
        unordered_set<char> hashMap;
        int ans = 0;
        for(int end = 0; end < n; end++){
            while(hashMap.count(s[end])){
                // 这里注意是把s[start]移除
                hashMap.erase(s[start]);
                start++;
            }
            hashMap.insert(s[end]);
            ans = max(ans,end - start + 1);
        }
        return ans;
    }
};
```



## 11盛最多水的容器

以两边为容器壁，往中间遍历

更新容器壁小的那个指针

```C++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int l = 0, r = height.size() - 1;
        int ans = 0;
        while(l < r){
          	// 根据题意，这里容器宽度为 r - l 不是r - l + 1
            ans = max(ans, (r - l) * min(height[l],height[r]));
            if(height[l] <= height[r])
                l++;
            else
                r--;
        }
        return ans;
    }
};
```



## 15 三数之和

```C++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        vector<vector<int>> ans;
        for (int first = 0; first < n; first++) {
            if (first > 0 && nums[first] == nums[first - 1])
                continue;
            int target = -nums[first];
            int third = n - 1;
            for (int second = first + 1; second < n; second++) {
                if (second > first + 1 && nums[second] == nums[second - 1])
                    continue;
                // 双指针需要保证左指针second < 右指针third
                while (second < third && nums[second] + nums[third] > target){
                    third--;
                }
                // 如果两者相同，则说明不再存在b<c，与我们的前提假设b>c矛盾，因此结束
                if(second == third)
                    break;
                if(nums[second] + nums[third] == target)
                    ans.push_back({nums[first],nums[second],nums[third]});
            }
        }
        return ans;
    }
};
```



## 17 电话号码的字母组合

```C++
class Solution {
private:
    vector<string> ans;
    string res;
    unordered_map<char, string> phoneMap{
        {'2', "abc"}, {'3', "def"},  {'4', "ghi"}, {'5', "jkl"},
        {'6', "mno"}, {'7', "pqrs"}, {'8', "tuv"}, {'9', "wxyz"}};
    void dfs(vector<string>& ans, const unordered_map<char, string>& phoneMap,
             const string& digits, int index, string& res) {
        if (index == digits.size()) {
            ans.push_back(res);
            return;
        }
        char digit = digits[index];
        string s = phoneMap.at(digit);
        for (int i = 0; i < s.size(); i++) {
            res.push_back(s[i]);
            dfs(ans, phoneMap, digits, index + 1, res);
            res.pop_back();
        }
    }

public:
    vector<string> letterCombinations(string digits) {
        if (digits.empty())
            return ans;
        dfs(ans, phoneMap, digits, 0, res);
        return ans;
    }
};
```



## 21 合并两个升序链表

### 解法一：递归

```
s1: A->B->C->...
s2: a->b->c->...
```

假设有链表s1和s2，如果A < a，则本次比较得到链表的下一个元素为A，并且下一次B和a比较；否则，本次比较得到链表的下一个元素为a，下一次A和b比较

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        if(!list1)
            return list2;
        if(!list2)
            return list1;
        if(list1->val < list2->val){
            list1->next = mergeTwoLists(list1->next, list2);
            return list1;
        }
        else{
            list2->next = mergeTwoLists(list1, list2->next);
            return list2;
        }
    }
};
```

**时间复杂度：O(n+m)**，其中 n 和 m 分别为两个链表的长度。因为每次调用递归都会去掉 l1 或者 l2 的头节点（直到至少有一个链表为空），函数 mergeTwoList 至多只会递归调用每个节点一次。因此，时间复杂度取决于合并后的链表长度，即 O(n+m)。

**空间复杂度：O(n+m)**，其中 n 和 m 分别为两个链表的长度。递归调用 mergeTwoLists 函数时需要消耗栈空间，栈空间的大小取决于递归调用的深度。结束递归调用时 mergeTwoLists 函数最多调用 n+m 次，因此空间复杂度为 O(n+m)。

### 解法二：迭代

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode* head = new ListNode();
        ListNode* tail = head;
        while (list1 != nullptr && list2 != nullptr) {
            if (list1->val < list2->val) {
                tail->next = list1;
                list1 = list1->next;
            } else {
                tail->next = list2;
                list2 = list2->next;
            }
            tail = tail->next;
        }
        tail->next = list1 == nullptr ? list2 : list1;
        return head->next;
    }
};
```

**时间复杂度：O(n+m)**，其中 n 和 m 分别为两个链表的长度。因为每次循环迭代中，l1 和 l2 只有一个元素会被放进合并链表中， 因此 while 循环的次数不会超过两个链表的长度之和。所有其他操作的时间复杂度都是常数级别的，因此总的时间复杂度为 O(n+m)。

**空间复杂度：O(1)**。我们只需要常数的空间存放若干变量。

## 22 括号生成

### 递归版

如果左括号数小于对数，选择左括号

如果右括号个数小于左括号，选择右括号

```C++
class Solution {
private:
    void dfs(vector<string>& ans, string& res, int left, int right, int n) {
        if (res.size() == n * 2) {
            ans.push_back(res);
            return;
        }
        if (left < n) {
            res.push_back('(');
            dfs(ans, res, left + 1, right, n);
            res.pop_back();
        }
        if (right < left) {
            res.push_back(')');
            dfs(ans, res, left, right + 1, n);
            res.pop_back();
        }
    }

public:
    vector<string> generateParenthesis(int n) {
        vector<string> ans;
        string res;
        dfs(ans, res, 0, 0, n);
        return ans;
    }
};
```



## 23 合并K个升序链表

https://leetcode.cn/problems/merge-k-sorted-lists/description/

本题在**21 合并两个升序链表**的基础上进行加难

### 解法一：顺序合并

每相邻两个合并升序链表

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
private:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2){
        ListNode* head = new ListNode();
        ListNode* tail = head;
        while(list1 != nullptr && list2 != nullptr){
            if(list1->val < list2->val){
                tail->next = list1;
                list1 = list1->next;
            }
            else{
                tail->next = list2;
                list2 = list2->next;
            }
            tail = tail->next;
        }
        tail->next = list1 == nullptr ? list2 : list1;
        return head->next;
    }
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        ListNode* ans = nullptr;
        for(int i = 0; i < lists.size(); i++){
            ans = mergeTwoLists(ans, lists[i]);
        }
        return ans;
    }
};
```



### 解法二： 分治合并

类似于快速排序，分组合并

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
private:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2){
        ListNode* head = new ListNode();
        ListNode* tail = head;
        while(list1 != nullptr && list2 != nullptr){
            if(list1->val < list2->val){
                tail->next = list1;
                list1 = list1->next;
            }
            else{
                tail->next = list2;
                list2 = list2->next;
            }
            tail = tail->next;
        }
        tail->next = list1 == nullptr ? list2 : list1;
        return head->next;
    }
    ListNode* merge(vector<ListNode*>& lists,int l, int r){
        if(l == r)
            return lists[l];
        if(l > r)
            return nullptr;
        int mid = (l + r) >> 1;
        return mergeTwoLists(merge(lists, l, mid), merge(lists, mid + 1, r));
    }
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        return merge(lists, 0, lists.size() - 1);
    }
};
```

### 解法三：优先级队列

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
private:
    struct Status{
        int val;
        ListNode* ptr;
        
    };
    struct cmp{
        bool operator () (const Status& s1, Status &s2)  const {
            return s1.val > s2.val;
        }
    };

    priority_queue<Status,vector<Status>,cmp> q;
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        ListNode* head = new ListNode();
        ListNode* tail = head;
        for(auto node : lists){
            if(node)
                q.push({node->val, node});
        }
        while(!q.empty()){
            Status s = q.top();
            q.pop();
            tail->next = s.ptr;
            tail = tail->next;
            if(s.ptr->next)
                q.push({s.ptr->next->val, s.ptr->next});
        }
        return head->next;
    }
};
```

或写成：

区别只是在于优先级队列自定义比较的不同写法

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
#include <queue>
class Solution {
private:
    struct Status{
        int val;
        ListNode* ptr;
        // 一定要原封不动的这样写
        bool operator < (const Status&s) const{
            return val > s.val;
        }
    };
    // 一定要原封不动的这样写
    priority_queue<Status> q;
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        ListNode* head = new ListNode();
        ListNode* tail = head;
        for(auto node : lists){
            if(node)
                q.push({node->val, node});
        }
        while(!q.empty()){
            Status s = q.top();
            q.pop();
            tail->next = s.ptr;
            tail = tail->next;
            if(s.ptr->next)
                q.push({s.ptr->next->val, s.ptr->next});
        }
        return head->next;
    }
};
```



## 25 K个一组翻转链表

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
private:
    ListNode* reverse(ListNode* head){
        ListNode* prev = nullptr;
        ListNode* cur = head;
        while(cur){
            ListNode* nxt = cur->next;
            cur->next = prev;
            prev = cur;
            cur = nxt;
        }
        return prev;
    }
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        // 增加一个dummyNode
        ListNode* dummyNode = new ListNode();
        dummyNode->next = head;
        // prev指向上一组的第k个节点，即本组第一个节点的前一个节点
        // 起到dummyNode的作用
        ListNode* prev = dummyNode, *end = prev;
        // 当end没有下一个节点，不需要反转，结束循环
        while(end->next){
            // end指向本组的第k个节点
            // 如果还遍历第[1,k]个节点，end为空，不需要反转，结束循环
            for(int i = 0; i < k && end; i++)
                end = end->next;
            // 如果循环因为某节点为空而停止，不需要反转，结束循环
            if(end == nullptr)
                break;
            // start指向本组的第一个节点
            ListNode* start = prev->next;
            // nxt指向下一组的第一个节点
            ListNode* nxt = end->next;
            // 反转链表，结果为新的链表头
            // 反转链表与上一组链表相连
            end->next = nullptr;
            prev->next = reverse(start);
            // 更新指针
            start->next = nxt;
            prev = start;
            end = prev;
        }
        return dummyNode->next;
    }
};
```



## 39 组合总和II

```C++
class Solution {
private:
    vector<int> res;
    vector<vector<int>> ans;
    void dfs(const vector<int>& candidates, vector<int>& res, int target,
             int index) {
        if (index == candidates.size()) {
            return;
        }
        if (target == 0) {
            ans.push_back(res);
            return;
        }
        // 不选这个数
        dfs(candidates, res, target, index + 1);
        // 选这个数
        if (candidates[index] <= target) {
            res.push_back(candidates[index]);
            dfs(candidates, res, target - candidates[index], index);
            res.pop_back();
        }
    }

public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        dfs(candidates, res, target, 0);
        return ans;
    }
};
```

## 42 接雨水

接雨水的本质：找到第i个位置左侧高度最大值leftMax和右侧高度最大值rightMax，取min(leftMax, rightMax) - height[i]为i当前可接的雨水

由以上分析可知，只与leftMax和rightMax中的最小值有关，与其中的最大值无关

### 单调栈法

```C++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        stack<int> stk;
        int ans = 0;
        for(int i = 0; i < n; i++){
            // 这里注意用的是while 不是 if
            while(!stk.empty() && height[i] > height[stk.top()]){
                int idx = stk.top();
                stk.pop();
                // 记得判断边界条件
                if(stk.empty())
                    break;
                int left = stk.top();
                int minHeight = min(height[left], height[i]);
                ans += (minHeight - height[idx]) * (i - left - 1);
            }
            stk.push(i);
        }
        return ans;
    }
};
```



### 双指针法

```C++
class Solution {
public:
    int trap(vector<int>& height) {
        int leftMax = 0, rightMax = 0;
        int left = 0, right = height.size() - 1;
        int ans = 0;
        while(left < right){
            leftMax = max(leftMax,height[left]);
            rightMax = max(rightMax,height[right]);
            if(leftMax < rightMax){
                ans += leftMax - height[left];
                ++left;
            }
            else{
                ans += rightMax - height[right];
                --right;
            }
        }
        return ans;
    }
};
```



## 45 跳跃游戏II

```C++
class Solution {
public:
    int jump(vector<int>& nums) {
        int n = nums.size();
        int start = 0, end = start + 1;
        int ans = 0;
        while(end < n){
            int nextStep;
            for(int i = start; i < end; i++){
                nextStep = max(nextStep, i + nums[i]);
            }
            ans++;
            start = end;
            end = nextStep + 1;
        }
        return ans;
    }
};
```

优化版

```C++
class Solution {
public:
    int jump(vector<int>& nums) {
        int n = nums.size();
        // end为边界下标，当前i能遍历的最大下标值
        int end = 0;
        int nextStep = 0;
        int ans = 0;
        // 问到nums[n - 1]需要的最小跳跃次数，因此不需要计算第n - 1个
        for(int i = 0; i < n; i++){
            nextStep = max(nextStep, i + nums[i]);
            if(i == end){
                ans++;
                end = nextStep;
            }
        }
        return ans;
    }
};
```



## 46 全排列

```C++
class Solution {
private:
    vector<vector<int>> ans;
    void dfs(vector<int>& nums,int idx){
        if(idx == nums.size() - 1){
            ans.push_back(nums);
            return;
        } 
        for(int i = idx; i < nums.size(); i++){
            swap(nums[i], nums[idx]);
            dfs(nums, idx + 1);
            swap(nums[i], nums[idx]);
        }
    }
public:
    vector<vector<int>> permute(vector<int>& nums) {
        dfs(nums, 0);
        return ans;
    }
};
```

## 49 字母异位词分组

第一种做法：对字符串进行排序，同一组异位词排序后的结果一定相同

```C++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        // 每个排序后的key对应的乱序的str
        unordered_map<string, vector<string>> hashMap;
        for (string& str : strs) {
            string key = str;
            sort(key.begin(), key.end());
            hashMap[key].push_back(str);
        }
        vector<vector<string>> ans;
        for (auto it = hashMap.begin(); it != hashMap.end(); it++) {
            ans.push_back(it->second);
        }
        return ans;
    }
};
```

第二种做法：同一组异位词每个字符出现的次数一定相同

```C++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> map;
        for (string str : strs) {
          	// 如果写成int counts[26];过不了
          	// 可能是因为编译优化
            int counts[26] = {0};
            for (char c : str) {
                counts[c - 'a']++;
            }
            string key = "";
            for (int i = 0; i < 26; ++i) {
                if (counts[i]) {
                    key.push_back(i + 'a');
                    key.push_back(counts[i]);
                }
            }
            map[key].push_back(str);
        }
        vector<vector<string>> res;
        for (auto it = map.begin(); it != map.end(); it++) {
            res.push_back(it->second);
        }
        return res;
    }
};
```

## 53 最大子数组和

```C++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n,0);
        dp[0] = nums[0];
        int ans = dp[0];
        for(int i = 1; i < n; i++){
            if(dp[i - 1] >= 0)
                dp[i] = dp[i - 1] + nums[i];
            else
                dp[i] = nums[i];
            ans = max(ans, dp[i]);
        }
        return ans;
    }
};
```



## 55 跳跃游戏

维护最大可达位置nextStep

如果当前位置i <= nextStep，说明当前位置i可达，可跳到当前位置i上，更新最大可达位置

如果最大可达位置比n - 1大，说明能够到达n - 1

```C++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        int nextStep = 0;
        for(int i = 0; i < n; i++){
            if(i <= nextStep){
                nextStep = max(nextStep, i + nums[i]);
                if(nextStep >= n - 1)
                    return true;
            }
        }
        return false;
    }
};
```



## 70 爬楼梯

https://leetcode.cn/problems/climbing-stairs/description/

### 解法一：递归

```C++
class Solution {
public:
    int climbStairs(int n) {
        if(n <= 1)
            return 1;
        return climbStairs(n - 1) + climbStairs(n - 2);
    }
};
```

**时间复杂度：$O(2^n)$**

可视为二叉树，树高为n，节点数为$2^n$，遍历搜索树需要$2^n$次

**空间复杂度：$O(n)$**

n个栈空间

### 解法二：记忆化搜索

```c++
class Solution {
private:
    vector<int> m;
    int dfs(int n){
        if(n <= 1)
            return 1;
        int &res = m[n];
        if(res)
            return res;
        return dfs(n - 1) + dfs(n - 2);
    }
public:
    int climbStairs(int n) {
        m.resize(n + 1);
        return dfs(n);
    }
};
```

**时间复杂度：$O(n)$**

每个状态只会计算一次，共n个状态

**空间复杂度：$O(n)$**

n个栈空间

### 解法三：动态规划

```c++
class Solution {
public:
    vector<int> dp;
    int climbStairs(int n) {
        dp.resize(n + 1);
        dp[0] = 1;
        dp[1] = 1;
        for(int i = 2; i <= n; i++)
            dp[i] = dp[i - 1] + dp[i - 2];
        return dp[n];
    }
};
```

**时间复杂度：$O(n)$**

状态数n * 状态计算1次

**空间复杂度：$O(n)$**

n个状态

### 解法四：空间优化

```c++
class Solution {
public:
    int climbStairs(int n) {
        int a = 0, b = 1, c;
        for(int i = 1; i <= n; i++){
            c = a + b;
            a = b;
            b = c;
        }
        return c;
    }
};
```

**时间复杂度：$O(n)$**

状态数n * 状态计算1次

**空间复杂度：$O(1)$**

只需3个变量

## 76 最小覆盖子串

```C++
class Solution {
public:
    string minWindow(string s, string t) {
        // hs记录s的字符和个数
        // ht记录t的字符和个数
        unordered_map<char,int> hs,ht;
        int sLen = s.size();
        int tLen = t.size();
        string res;
        if(sLen < tLen)
            return res;
        for(auto c: t){
            ht[c]++;
        }
        // cnt用于记录需要的字符数
        int cnt = 0;
        // 遍历s
        for(int i = 0, j = 0; i < sLen; i++){
            hs[s[i]]++;
            // 如果当前窗口内hs含有字符s[i]个数比ht少
            // 说明当前字符s[i]是必须的
            // 说明窗口需要继续扩展
            if(hs[s[i]] <= ht[s[i]])
                cnt++;
            // s[j]是多余的
            // 收缩窗口
            while(hs[s[j]] > ht[s[j]]){
                hs[s[j]]--;
                j++;
            }
            if(cnt == t.size()){
                if(res.empty() || i - j + 1 < res.size())
                    res = s.substr(j, i - j + 1);
            }
        }
        return res;
    }
};
```



## 78 子集

### 迭代法

用二进制表示每个幂集，第i位为1表示选择第i个元素，那么总共有2^n - 1个状态

```C++
class Solution {
private:
    vector<int> t;
    vector<vector<int>> ans;
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        int n = nums.size();
        // 总共有2^n - 1种状态
        for(int mask = 0; mask < (1 << n); mask++){
            // 每个状态对应的序列不同
            t.clear();
            // 枚举每个元素
            for(int i = 0; i < n; i++){
                // 1 << i表示第i位为1
                // mask & (1 << i)表示mask状态第i位为1
                // 即选择第i个元素
                if(mask & (1 << i)){
                    t.push_back(nums[i]);
                }
            }
            ans.push_back(t);
        }
        return ans;
    }
};
```



### 递归法

用idx记录当前遍历到第几个元素，分类讨论：

- 选择当前元素，如果选择当前元素，那么dfs之后需要回溯
- 不选择当前元素

```C++
class Solution {
private:
    vector<vector<int>> ans;
    vector<int> res;
    void dfs(vector<int>&nums, int idx){
        if(idx == nums.size()){
            ans.push_back(res);
            return;
        }
        res.push_back(nums[idx]);
        dfs(nums, idx + 1);
        res.pop_back();
        dfs(nums, idx + 1);
    }
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        dfs(nums, 0);
        return ans;
    }
};
```



## 79 单词搜索

```C++
class Solution {
private:
    int rows, cols;
    int dx[4] = {-1, 0, 1, 0};
    int dy[4] = {0, 1, 0, -1};
    bool dfs(vector<vector<char>>& board, const string& word, int x, int y,
             int idx) {
        if (board[x][y] != word[idx])
            return false;
        if (idx == word.size() - 1)
            return true;
        for (int i = 0; i < 4; i++) {
            int a = x + dx[i];
            int b = y + dy[i];
            if (a >= 0 && a < rows && b >= 0 && b < cols) {
                board[x][y] = '\0';
                if (dfs(board, word, a, b, idx + 1))
                    return true;
                board[x][y] = word[idx];
            }
        }
        return false;
    }

public:
    bool exist(vector<vector<char>>& board, string word) {
        rows = board.size();
        cols = board[0].size();
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (dfs(board, word, i, j, 0))
                    return true;
            }
        }
        return false;
    }
};
```



## 84 柱状图最大的矩形

https://leetcode.cn/problems/largest-rectangle-in-histogram/description/

### 解法一： 暴力枚举

枚举宽：枚举左边界，再枚举右边界

```c++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int ans = 0;
        int n = heights.size();
        for(int i = 0; i < n; i++){
            int minHeight = heights[i];
            for(int j = i; j < n; j++){
                minHeight = min(minHeight, heights[j]);
                ans = max(ans, (j - i + 1) * minHeight);
            }
        }
        return ans;
    }
};
```

枚举高：

```C++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int ans = 0;
        int n = heights.size();
        // 枚举每根柱子的高度
        for(int mid = 0; mid < n; mid++){
            int height = heights[mid];
            int left = mid, right = mid;
            // 如果左右柱子比当前枚举的柱子高，那么矩形的高度仍然为当前枚举柱子的高度heights[mid]
            while(left - 1 >= 0 && heights[left - 1] >= height) --left;
            while(right + 1 < n && heights[right + 1] >= height) ++right;
          	// 注意这里left和right都指向最后一个比枚举柱子高度大的柱子
          	// 与单调栈做法不同，这里为right - left + 1（单调栈是right - left - 1）
            ans = max(ans, (right - left + 1) * height);
        }
        return ans;
    }
};
```

可以发现，这两种暴力方法的时间复杂度均为$ O(N^2)$，会超出时间限制，我们必须要进行优化。考虑到枚举「宽」的方法使用了两重循环，本身就已经需要 $O(N^2)$的时间复杂度，不容易优化，因此我们可以考虑优化只使用了一重循环的枚举「高」的方法。

### 解法二： 单调栈

#### 朴素版

> 单调栈常用于查找比它大的第一个数 or 比它小的第一个数
>
> 单调递增栈：栈内离栈顶越近（下标越大），栈元素越大；栈顶元素为栈内最大
>
> - 当有新元素进入，如果新元素比栈顶元素大，直接入栈。
> - 当有新元素进入，如果新元素比栈顶元素小，栈弹出，直到满足新元素比栈顶元素大，再将新元素入栈。
>
> 由此可见，新元素C使栈顶元素A弹出后，下一个栈顶元素B是A左边第一个比B小的元素，新元素C是A右边第一个比C小的元素

由暴力枚举高的解法可知道，我们枚举高度后需要分别往两边寻找比它矮的第一个柱子（数），因此可以使用单调栈

```c++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int ans = 0;
        int n = heights.size();
        vector<int> left(n), right(n);
        stack<int> stk;
        for(int i = 0; i < n; i++){
            while(!stk.empty() && heights[stk.top()] >= heights[i]) stk.pop();
            left[i] = (stk.empty() ? -1 : stk.top());
            stk.push(i);
        }
        stk = stack<int>();
        for(int i = n - 1; i >= 0; i--){
            while(!stk.empty() && heights[stk.top()] >= heights[i]) stk.pop();
            right[i] = (stk.empty() ? n : stk.top());
            stk.push(i);
        }
        for(int i = 0; i < n; i++){
          	// right 和 left 都指向第一个比枚举柱子小的柱子，因此不包含left 和 right
          	// 宽度计算为： (right - 1) - (left + 1) + 1 = right - left - 1
            ans = max(ans, (right[i] - left[i] - 1) * heights[i]);
        }
        return ans;
    }
};
```

**复杂度分析**

- 时间复杂度：*O*(*N*)。
- 空间复杂度：*O*(*N*)。

每一个位置只会入栈一次（在枚举到它时），并且最多出栈一次。

因此当我们从左向右/总右向左遍历数组时，对栈的操作的次数就为 *O*(*N*)。所以单调栈的总时间复杂度为 *O*(*N*)。

#### 优化版

对上述进行优化

> 新元素C使栈顶元素A弹出后，下一个栈顶元素B是A左边第一个比B小的元素，新元素C是A右边第一个比C小的元素

```C++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int len = heights.size() + 2;
        vector<int> height(len);
      	// 在heights数组基础上在头尾加上0元素
      	// 这是因为以heights[0]（heights[heights.size() - 1]）作为柱子高度时，缺少左边界（右边界）
      	// 为其补上左右边界，使其能够计算heights[0]（heights[heights.size() - 1]）作为柱子高度时的面积
        height[0] = 0;
        height[len - 1] = 0;
        for(int i = 1; i < len - 1; i++)
            height[i] = heights[i - 1];
        stack<int> stk;
        int ans = 0;
        for(int i = 0; i < len; i++){
            while(!stk.empty() && height[i] < height[stk.top()]){
              	// 新元素i让栈弹出，栈顶元素为stk.top()
                int idx = stk.top();
                stk.pop();
              	// 这里的stk.top()为弹出后的下一个栈顶元素
                int w = i - stk.top() - 1;
              	// 高度为最初的栈顶元素
                ans = max(ans, w * height[idx]);
            }
          	// 没有弹出时不需要计算面积
            stk.push(i);
        }
        return ans;
    }
};
```

**复杂度分析**

- 时间复杂度：*O*(*N*)。
- 空间复杂度：*O*(*N*)。

## 85 最大矩形

https://leetcode.cn/problems/maximal-rectangle/

若`matrix[i][j] == ‘1’`，那么以该点为矩形的右下角

- 记`left[i][j]`为第i行第j列元素左边连续1的个数（包括第i行第j列）
- 那么矩形可以转换为若干个柱形，`left[i][j]`为柱形的高度，题目就变成了84 求柱状图最大的柱形
- 宽度为k - i + 1，k为第i行前的第k行

换一下坐标轴，宽度就是代码中的高度，高度就是代码中的宽度

### 解法一：暴力枚举点

```C++
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        int n = matrix.size();
        int m = matrix[0].size();
        vector<vector<int>> left(n,vector<int>(m, 0));
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (matrix[i][j] == '1') {
                    left[i][j] = (j == 0 ? 0: left[i][j - 1]) + 1;
                }
            }
        }
        int ans = 0;
        for(int i = 0; i < n; i++){
            for(int j = 0; j < m; j++){
                if(matrix[i][j] == '1'){
                    int w = left[i][j];
                  	// 这里area初始化为w, 因为假设matrix[k][j]都为'0', 宽度至少为1
                    int area = w;
                    for(int k = i - 1; k >= 0; k--){
                        w = min(w, left[k][j]);
                        area = max(area, (i - k + 1) * w);
                    }
                    ans = max(ans, area);
                }
            }
        }
        return ans;
    }
};
```

复杂度分析

时间复杂度：$O(m^2n)$，其中 m 和 n 分别是矩阵的行数和列数。计算 left 矩阵需要 O(mn) 的时间。随后对于矩阵的每个点，需要 O(m) 的时间枚举高度。故总的时间复杂度为$ O(mn)+O(mn)⋅O(m)=O(m^2n)$。

空间复杂度：$O(mn)$，其中 m 和 n 分别是矩阵的行数和列数。我们分配了一个与给定矩阵等大的数组，用于存储每个元素的左边连续 1 的数量。

### 解法二：单调栈

同84 一样，需要求出柱形图最小的高度（代码中为宽度）

与84不同之处在于，本题可以拆分为m列 柱形图，将84题的代码处理m次即可

```c++
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        int n = matrix.size();
        int m = matrix[0].size();
        vector<vector<int>> left(n, vector<int>(m, 0));
        for(int i = 0; i < n; i++){
            for(int j = 0; j < m; j++){
                if(matrix[i][j] == '1')
                    left[i][j] = (j == 0 ? 0 : left[i][j - 1]) + 1;
            }
        }
        int ans = 0;
        int len = n + 2;
      	// 枚举每一列
        for(int j = 0; j < m; j++){
            stack<int> stk;
            int area = 0;        
            vector<int> ll(len);
            ll[0] = 0;
            ll[len - 1] = 0;
          	// 初始化别弄反了，是left[k - 1][j]，不是left[j][k - 1]
            for(int k = 1; k < len - 1; k++)
                ll[k] = left[k - 1][j];
            for(int i = 0; i < len; i++){
                while(!stk.empty() && ll[i] < ll[stk.top()]){
                    int idx = stk.top();
                    stk.pop();
                    int w = i - stk.top() - 1;
                    area = max(area, w * ll[idx]);
                }
                stk.push(i);
            }
            ans = max(ans, area);
        }
        return ans;
    }
};
```

复杂度分析

时间复杂度：O(mn)，其中 m 和 n 分别是矩阵的行数和列数。计算 left 矩阵需要 O(mn) 的时间；对每一列应用柱状图算法需要 O(m) 的时间，一共需要 O(mn) 的时间。

空间复杂度：O(mn)，其中 m 和 n 分别是矩阵的行数和列数。我们分配了一个与给定矩阵等大的数组，用于存储每个元素的左边连续 1 的数量。

## 112 路径总和

https://leetcode.cn/problems/path-sum/description/

### 做法：递归

本做法中的targetSum为**剔除掉当前节点剩余的targetSum**，即左/右节点到达子节点需要走的路径和

#### 思路版

该版本没有怎么简化，着重于详细的思路

```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
private:
    bool dfs(TreeNode* root, int targetSum){
      	// 判断是否存在左节点
        if(root->left){
          	// 如果左节点存在满足条件的路径，那么当前节点也存在满足条件的路径
            if(dfs(root->left, targetSum - root->left->val))
                return true;
        }
      	// 右节点同理
        if(root->right){
            if(dfs(root->right, targetSum - root->right->val))
                return true;
        }
      	// 如果当前节点为叶子节点
        if(!root->left && !root->right){
            if(targetSum == 0)
                return true;
            else 
                return false;
        }
        return false;       
    }
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
      	// 如果根节点没空，不存在题解
        if(!root)
            return false;
        return dfs(root, targetSum - root->val);
    }
};
```

或写成

```C++
class Solution {
private:
    bool traversal(TreeNode* cur, int count) {
        if (!cur->left && !cur->right && count == 0) return true; // 遇到叶子节点，并且计数为0
        if (!cur->left && !cur->right) return false; // 遇到叶子节点直接返回

        if (cur->left) { // 左
            count -= cur->left->val; // 递归，处理节点;
            if (traversal(cur->left, count)) return true;
            count += cur->left->val; // 回溯，撤销处理结果
        }
        if (cur->right) { // 右
            count -= cur->right->val; // 递归，处理节点;
            if (traversal(cur->right, count)) return true;
            count += cur->right->val; // 回溯，撤销处理结果
        }
        return false;
    }

public:
    bool hasPathSum(TreeNode* root, int sum) {
        if (root == NULL) return false;
        return traversal(root, sum - root->val);
    }
};
```



#### 简化版

```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        if(root == NULL) return false;
        if(!root->left && !root->right && targetSum == root->val)   return true;
        return hasPathSum(root->left, targetSum - root->val) || hasPathSum(root->right, targetSum - root->val);
    }
};
```

## 121 买卖股票的最佳时机

贪心：找出最低的价格买入，找出最高的价格买出

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int cost = INT_MAX, profit = 0;
        for(int i = 0;i < prices.size(); i++){
            cost = min(cost, prices[i]);
            profit = max(profit, prices[i] - cost);
        }
        return profit;
    }
};
```

## 128 最长连续序列

关键：

- 对数组去重
- 如果x+y 的前驱x + y - 1也在，可以跳过当前的计算，因为x + y - 1已经计算过了

```C++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        // 对数组去重
        unordered_set<int> numSet;
        for(int num : nums)
            numSet.insert(num);
        int ans = 0;
        // 遍历每个元素
        for(int num : numSet){
            // 如果num的前驱num - 1存在说明可以跳过
            if(!numSet.count(num - 1)){
                int curNum = num;
                int len = 1;
                while(numSet.count(curNum + 1)){
                    ++curNum;
                    ++len;
                }
                ans = max(ans, len);
            }
        }
        return ans;
    }
};
```



## 141 环形链表

根据题意：一个节点没有环

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(head == nullptr || head->next == nullptr)
            return false;
        ListNode* fast = head, *slow = head;
        while(true){
            if(fast == nullptr || fast->next == nullptr)
                return false;
            fast = fast->next->next;
            slow = slow->next;
          	if(fast == slow) 
              	break;
        }
        return true;
    }
};
```

## 142 环形链表II

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if(head == nullptr || head->next == nullptr)
            return nullptr;
        ListNode* fast = head, *slow = head;
        while(true){
            if(fast == nullptr || fast->next == nullptr)
                return nullptr;
            fast = fast->next->next;
            slow = slow->next;
            if(fast == slow)    break;
        }
        fast = head;
        while(slow != fast){
            fast = fast->next;
            slow = slow->next;
        }
        return fast;
    }
};
```

## 146 LRU缓存

```C++
class DListNode {
public:
    int key;
    int value;
    DListNode* prev;
    DListNode* next;
    DListNode():key(0), value(0),prev(nullptr),next(nullptr){};
    DListNode(int key_, int value_) : key(key_), value(value_),prev(nullptr),next(nullptr) {}
};

class LRUCache {
private:
    DListNode* head;
    DListNode* tail;
    unordered_map<int, DListNode*> cache;
    int size;
    int capacity;

    void addToHead(DListNode* node) {
        node->prev = head;
        node->next = head->next;
        head->next->prev = node;
        head->next = node;
    }

    void removeNode(DListNode* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }

    void moveToHead(DListNode* node) {
        removeNode(node);
        addToHead(node);
    }

    DListNode* removeTail() {
        DListNode* node = tail->prev;
        removeNode(node);
        return node;
    }

public:
    LRUCache(int capacity) {
        this->capacity = capacity;
        head = new DListNode();
        tail = new DListNode();
        head->next = tail;
        tail->prev = head;
        size = 0;
    }

    int get(int key) {
        if (!cache.count(key))
            return -1;
        DListNode* node = cache[key];
        moveToHead(node);
        return node->value;
    }

    void put(int key, int value) {
        if (cache.count(key)) {
            DListNode* node = cache[key];
            moveToHead(node);
            node->value = value;
        } else {
            DListNode* node = new DListNode(key, value);
            addToHead(node);
            cache[key] = node;
            ++size;
            if (size > capacity) {
                DListNode* t = removeTail();
                cache.erase(t->key);
                delete t;
                --size;
            }
        }
    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```



## 160 相交链表

https://leetcode.cn/problems/intersection-of-two-linked-lists/?envType=study-plan-v2&envId=top-100-liked

拼接链表A和链表B，拼接后，两个链表同时开始往尾部遍历，

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* a = headA, *b = headB;
        while(a != b){
            a = a != nullptr ? a->next : headB;
            b = b != nullptr ? b->next : headA;
        }
        return a;
    }
};
```



## 200 岛屿数量

> 题解中这里修改了原数组，大家如果面试的时候，要问清楚面试官是否能修改原数组，不能的话就得加入标记数组，不要一给题就直接上手

### 解法一： dfs

我的解法

```C++
class Solution {
private:
    int dx[4] = {-1, 0, 1, 0};
    int dy[4] = {0, 1, 0, -1};
    void dfs(vector<vector<char>>& grid, vector<vector<bool>>& isVisited, int x,
             int y) {
        int n = grid.size();
        int m = grid[0].size();
        for (int i = 0; i < 4; i++) {
            int a = x + dx[i];
            int b = y + dy[i];
            if (a >= 0 && a < n && b >= 0 && b < m && grid[a][b] == '1' &&
                !isVisited[a][b]) {
                isVisited[a][b] = true;
                dfs(grid, isVisited, a, b);
            }
        }
    }

public:
    int numIslands(vector<vector<char>>& grid) {
        int n = grid.size();
        int m = grid[0].size();
        vector<vector<bool>> isVisited(n, vector<bool>(m, false));
        int ans = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == '1' && !isVisited[i][j]) {
                    ans++;
                    isVisited[i][j] = true;
                    dfs(grid, isVisited, i, j);
                }
            }
        }
        return ans;
    }
};
```

复杂度分析

时间复杂度：O(MN)，其中 M 和 N 分别为行数和列数。

空间复杂度：O(MN)，在最坏情况下，整个网格均为陆地，深度优先搜索的深度达到 MN。

### 解法二： bfs

我的解法

与dfs类似

```C++
class Solution {
private:
    typedef pair<int,int> PII;
    int dx[4] = {-1, 0, 1, 0};
    int dy[4] = {0, 1, 0, -1};
    void bfs(vector<vector<char>>& grid, vector<vector<bool>>& isVisited, int x, int y){
        int n = grid.size();
        int m = grid[0].size();
        queue<PII> q;
        q.push({x, y});
        while(!q.empty()){
            auto t = q.front();
            q.pop();
            for(int i = 0; i < 4; i++){
                int a = t.first + dx[i];
                int b = t.second + dy[i];
                if(a >= 0 && a < n && b >=0 && b < m && grid[a][b] == '1' && !isVisited[a][b]){
                    isVisited[a][b] = true;
                    q.push({a, b});
                }
            }
        }
    }
public:
    int numIslands(vector<vector<char>>& grid) {
        int n = grid.size();
        int m = grid[0].size();
        vector<vector<bool>> isVisited(n, vector<bool>(m, false));
        int ans = 0;
        for(int i = 0; i < n; i++){
            for(int j = 0; j < m; j++){
                if(grid[i][j] == '1' && !isVisited[i][j]){
                    ans++;
                    bfs(grid, isVisited, i, j);
                }
            }
        }
        return ans;
    }
};
```

**复杂度分析**

时间复杂度：O(MN)，其中 M 和 N 分别为行数和列数。

空间复杂度：O(min(M,N))，在最坏情况下，整个网格均为陆地，队列的大小可以达到 min(M,N)。

### 解法三： 并查集

```C++
class Solution {
private:
    vector<int> p;
    vector<bool> isVisited;
    int res;
    int dx[4] = {-1, 0, 1, 0};
    int dy[4] = {0, 1, 0, -1};
    int Find(int x) {
        if (p[x] != x)
            p[x] = Find(p[x]);
        return p[x];
    }
    void Union(int x, int y) {
      	// 每次合并都要res减一，因此如果合并过直接跳过，以免res多减
        if (Find(x) == Find(y))
            return;
        p[Find(x)] = Find(y);
        res--;
    }

public:
    int numIslands(vector<vector<char>>& grid) {
        int n = grid.size();
        int m = grid[0].size();
        p.resize(n * m);
        isVisited.resize(n * m);
        res = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                int idx = i * m + j;
                if (grid[i][j] == '1') {
                    p[idx] = idx;
                    res++;
                }
            }
        }
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                int idx = i * m + j;
                if (grid[i][j] == '1') {
                  	// 只有这个地方需要标记已遍历
                    isVisited[idx] = true;
                    for (int k = 0; k < 4; k++) {
                        int a = i + dx[k];
                        int b = j + dy[k];
                        if (a >= 0 && a < n && b >= 0 && b < m &&
                            grid[a][b] == '1' && !isVisited[a * m + b]) {
                          	// 这里不需要标记遍历，因为Union不会继续往下访问下一个节点
                          	// 不是递归函数！
                            Union(idx, a * m + b);
                        }
                    }
                }
            }
        }
        return res;
    }
};
```

复杂度分析

时间复杂度：O(MN×α(MN))，其中 M 和 N 分别为行数和列数。注意当使用路径压缩（见 find 函数）和按秩合并（见数组 rank）实现并查集时，单次操作的时间复杂度为 α(MN)，其中 α(x) 为反阿克曼函数，当自变量 x 的值在人类可观测的范围内（宇宙中粒子的数量）时，函数 α(x) 的值不会超过 5，因此也可以看成是常数时间复杂度。

空间复杂度：O(MN)，这是并查集需要使用的空间。

> 我没有使用按秩合并

## 206 反转链表

### 迭代版

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* cur = head;
        while(cur){
            ListNode* next = cur->next;
            cur->next = prev;
            prev = cur;
            cur = next;
        }
      	// cur指向nullptr, prev指向原链表最后一个元素（新链表头节点）
        return prev;
    }
};
```

**复杂度分析**

- 时间复杂度：*O*(*n*)，其中 *n* 是链表的长度。需要遍历链表一次。
- 空间复杂度：*O*(1)。

### 递归版

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(!head || !head->next)
            return head;
        ListNode* newHead = reverseList(head->next);
        head->next->next = head;
        head->next = nullptr;
        return newHead;
    }
};
```

**复杂度分析**

时间复杂度：O(n)，其中 n 是链表的长度。需要对链表的每个节点进行反转操作。

空间复杂度：O(n)，其中 n 是链表的长度。空间复杂度主要取决于递归调用的栈空间，最多为 n 层。

## 209 长度最小的子数组

https://leetcode.cn/problems/minimum-size-subarray-sum/description/

题目关键在于：这个子数组是连续的

### 解法一：暴力枚举

先枚举子数组的起点，再往后枚举，满足sum >= target停止，计算子数组长度 j - i + 1

```C++
class Solution {
private:
    const int INF = 0x3f3f3f3f;
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int n = nums.size();
        int ans = INF;
        for(int i = 0; i < n; i++){
            int sum = 0;
            for(int j = i; j < n; j++){
                sum += nums[j];
                if(sum >= target)
                    ans = min(ans, j - i + 1);
            }
        }
        if(ans == INF) 
            return 0;
        else
            return ans;
    }
};
```

复杂度分析

时间复杂度：$O(n^2)$，其中 n 是数组的长度。需要遍历每个下标作为子数组的开始下标，对于每个开始下标，需要遍历其后面的下标得到长度最小的子数组。

空间复杂度：O(1)。

### 解法二： 前缀和 + 二分查找

解法一枚举起点之后又向后枚举了n个元素才找到sum >= target

由于本题限定了“正整数元素”，对于这种找到第一个满足sum >= target的元素，显然可以使用二分查找

我们的目标是sum >= target 而不是num >= target，因此需要一个数组记录每个元素作为末尾时的sum[i]

因此想到前缀和

```C++
class Solution {
private:
    const int INF = 0x3f3f3f3f;

public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int n = nums.size();
        vector<int> s(n + 1);
        //  计算前缀和
        for (int i = 1; i <= n; i++) {
            // 这里原本是nums[i]，但是nums和s的下标之间偏移了1
            s[i] = s[i - 1] + nums[i - 1];
        }
        int ans = INF;
        // 遍历起点，由于是遍历前缀和，所以下标可直接从1开始
        for (int i = 1; i <= n; i++) {
            // 我们要找到sum >= target
            // sum = s[idx] - s[i - 1]
            // 因此二分查找的目标为在数组s中找到大于等于target + s[i -
            // 1]的第一个元素
            int t = target + s[i - 1];
            auto it = lower_bound(s.begin(), s.end(), t);
            if (it != s.end()) {
                int idx = distance(s.begin(), it);
                ans = min(ans, idx - i + 1);
            }
        }
        return ans == INF ? 0 : ans;
    }
};
```

二分自己实现如下：

```C++
class Solution {
private:
    const int INF = 0x3f3f3f3f;

public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int n = nums.size();
        vector<int> s(n + 1);
        //  计算前缀和
        for (int i = 1; i <= n; i++) {
            // 这里原本是nums[i]，但是nums和s的下标之间偏移了1
            s[i] = s[i - 1] + nums[i - 1];
        }
        int ans = INF;
        // 遍历起点，由于是遍历前缀和，所以下标可直接从1开始
        for (int i = 1; i <= n; i++) {
            // 我们要找到sum >= target
            // sum = s[idx] - s[i - 1]
            // 因此二分查找的目标为在数组s中找到大于等于target + s[i - 1]的第一个元素
            int t = target + s[i - 1];
            int l = i, r = n;
            while(l < r){
                int mid = l + r >> 1;
                if(s[mid] >= t) r = mid;
                else l = mid + 1;
            }
            if(s[l] >= t)
                ans = min(ans, l - i + 1);
        }
        return ans == INF ? 0 : ans;
    }
};
```

### 解法三：移动窗口

由于题目的子数组是连续的序列，求sum >= target子数组长度最小值，实际上是要求子数组滑动时的最小值 >= target

想让子数组长度最小，意味着这个移动窗口的长度不定，因此移动窗口的头部也需要改变

加进一个元素

```C++
class Solution {
private:
    const int INF = 0x3f3f3f3f;
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int n = nums.size();
        int start = 0, end = 0;
        int ans = INF;
        int sum = 0;
        while(end < n){
            sum += nums[end];
            while(sum >= target){
                ans = min(ans, end - start + 1);
                sum -= nums[start];
                start++;
            }
            end++;
        }
        return ans == INF ? 0 : ans;
    }
};
```

以上是双指针做法

如果套模板，使用队列解决，则是：

```C++
class Solution {
private:
    const int INF = 0x3f3f3f3f;
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int n = nums.size();
        queue<int> q;
        int sum = 0;
        int ans = INF;
        for(int i = 0; i < n; i++){
          	// 先加nums[i] 再对队头进行判断
            q.push(i);
            sum += nums[i];
          	// 处理队头
            while(!q.empty() && sum >= target){
                sum -= nums[q.front()];
                ans = min(ans, q.back() - q.front() + 1);
                q.pop();
            }
        }
        return ans == INF ? 0 : ans;
    }
};
```

需要注意的是，队列的头是早加入的元素，尾是刚刚加入的元素，即头对应start，尾对应end

## 234 回文链表

https://leetcode.cn/problems/palindrome-linked-list/solutions/457059/hui-wen-lian-biao-by-leetcode-solution/?envType=study-plan-v2&envId=top-100-liked

### 递归版

利用函数递归特性，当函数遍历到最底层遇到终止条件才会返回，终止条件设置为nullptr，则函数到达最后一个节点才会返回

此时我们设置firstPointer指向第一个节点，当函数开始返回时，将curNode与firstPointer的值进行比较

如果某次比较发现不相等，那么说明不是回文链表，需要向上传递结果，因此返回值设置为bool类型

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
private:
    ListNode* firstPointer;
public:
    bool recursivelyCheck(ListNode* curNode){
        if(!curNode)
            return true;
        if(!recursivelyCheck(curNode->next))
            return false;
        if(firstPointer->val != curNode->val)
            return false;
        firstPointer = firstPointer->next;
        return true;
    }
    bool isPalindrome(ListNode* head) {
        firstPointer = head;
        return recursivelyCheck(head);
    }
};
```

**复杂度分析**

- 时间复杂度：*O*(*n*)，其中 *n* 指的是链表的大小。
- 空间复杂度：*O*(*n*)，其中 *n* 指的是链表的大小。

### 迭代版

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
private:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* cur = head;
        while (cur) {
            ListNode* next = cur->next;
            cur->next = prev;
            prev = cur;
            cur = next;
        }
        return prev;
    }
    ListNode* getFirstEnd(ListNode* head) {
        ListNode *fast = head, *slow = head;
        while (fast->next != nullptr && fast->next->next != nullptr) {
            fast = fast->next->next;
            slow = slow->next;
        }
        return slow;
    }

public:
    bool isPalindrome(ListNode* head) {
        if (!head)
            return true;
        ListNode* firstEnd = getFirstEnd(head);
        ListNode* secondHead = reverseList(firstEnd->next);
        ListNode *p1 = head, *p2 = secondHead;
        while (p2) {
            if (p1->val != p2->val)
                return false;
            p1 = p1->next;
            p2 = p2->next;
        }
        // 如果需要恢复原状
        firstEnd->next = reverseList(secondHead);
        return true;
    }
};
```

**复杂度分析**

时间复杂度：O(n)，其中 n 指的是链表的大小。

空间复杂度：O(1)。我们只会修改原本链表中节点的指向，而在堆栈上的堆栈帧不超过 O(1)。

## 240 搜索二维矩阵II

https://leetcode.cn/problems/search-a-2d-matrix-ii/description/

### 解法一：暴力枚举

无脑解法，没有利用题意“升序”

```C++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        for(int i = 0; i < matrix.size(); i++){
            for(int j = 0; j < matrix[i].size(); j++){
                if(matrix[i][j] == target)
                    return true;
            }
        }
        return false;
    }
};
```

**复杂度分析**

- 时间复杂度：*O*(*mn*)。
- 空间复杂度：*O*(1)。

### 解法二：二分查找

利用题意“升序”可直接二分查找

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
      	// 这里要用引用，不然会报内存超出
        for(const auto &row : matrix){
            auto it = lower_bound(row.begin(), row.end(), target);
            if(it != row.end() && *it == target)   return true;
        }
        return false;
    }
};
```



### 解法三：

https://leetcode.cn/problems/search-a-2d-matrix-ii/solutions/2361487/240-sou-suo-er-wei-ju-zhen-iitan-xin-qin-7mtf

将矩阵逆时针旋转45度，将其变成二叉搜索树

以右上角为根节点

如果超出边界，说明无解

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int i = 0, j = matrix[0].size() - 1;
        while(i <= matrix.size() - 1 && j >= 0 ){
            if(matrix[i][j] == target) return true;
            if(matrix[i][j] < target) i++;
            else j--;
        }
        return false;
    }
};
```

## 279 完全平方数

思路：

题目要求和为i的完全平方数最小数量

假设选择了j (j <= i)，那么接下来要找的就是和为i - j*j的完全平方数最小数量

可以看出，这是一个状态递推式，可以使用动态规划

f[i - j* j]下标比f[i]小，所以可以从小到大遍历

```C++
class Solution {
public:
    int numSquares(int n) {
        // dp[i]表示和为i的完全平方数的最少数量
        // dp[0] = 0
        vector<int> dp(n + 1, 0);
        for(int i = 1; i <= n; i++){
            // 枚举完全平方数
            dp[i] = INT_MAX;
            for(int j = 1; j * j <=i; j++){
                dp[i] = min(dp[i],1 + dp[i - j * j]);
            }
        }
        return dp[n];
    }
};
```



## 283 移动零

```C++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int n = nums.size();
        // 右指针遍历数组的每个数组
        // 左指针指向非零序列的尾部（尾部元素的下一个）
        // [l,r - 1]一定为0
        int l = 0, r = 0;
        while(r < n){
            if(nums[r]){
                swap(nums[l],nums[r]);
                l++;
            }
            r++;
        }
    }
};
```



## 415 字符串相加（大数加法）

```C++
class Solution {
public:
    string addStrings(string num1, string num2) {
        int len1 = num1.size(), len2 = num2.size();
        if (len1 < len2)
            return addStrings(num2, num1);
      	// 将num1，num2反向存储，因为最高位放在数组末端，方便进位
        vector<int> a, b;
        for (int i = len1 - 1; i >= 0; i--)
            a.push_back(num1[i] - '0');
        for (int i = len2 - 1; i >= 0; i--)
            b.push_back(num2[i] - '0');
        string ans;
        int t = 0;
        for (int i = 0; i < len1; i++) {
            t += a[i];
            if (i < len2)
                t += b[i];
            ans.push_back(t % 10 + '0');
            t /= 10;
        }
        if (t)
            ans.push_back(t + '0');
     		// ans也为倒序，真实答案需要反过来
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```



## 438 找到字符串中的所有字母异位词

```C++
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        vector<int> ans;
        int sLen = s.size(), pLen = p.size();
        // 注意边界条件
        if(sLen < pLen)
            return ans;
        // 哈希表，统计字符数
        vector<int> sCount(26);
        vector<int> pCount(26);
        
        for (int i = 0; i < pLen; i++) {
            ++sCount[s[i] - 'a'];
            ++pCount[p[i] - 'a'];
        }
        // 这里使用了vector的比较
        if (sCount == pCount)
            ans.push_back(0);
        for (int i = 0; i < sLen - pLen; i++) {
            // 移动窗口
            --sCount[s[i] - 'a'];
            ++sCount[s[i + pLen] - 'a'];
            if(sCount == pCount)
                ans.push_back(i + 1);
        }
        return ans;
    }
};
```

## 470 用rand7() 实现rand10()

```C++
// The rand7() API is already defined for you.
// int rand7();
// @return a random integer in the range 1 to 7

class Solution {
public:
    int rand10() {
        while (true) {
            // (rand_X - 1) * Y + rand_Y 可构造[1, X * Y]
            // 相当于构造一个Y进制的数，个位是rand_Y，满足Y进制，每个个位等概率分布
            // “十位”是 (rand_X - 1)，也是等概率分布
            // rand_X为[1,X]
            // 类比于3 = 二进制的 11 = 2 * 1 + 2，这里Y就是2，X - 1就是1
            // 因此能够构造的最大值为 Y * (X - 1) + Y
            int num = (rand7() - 1) * 7 + (rand7() - 1); // num 为等概率[0, 48]
            // 对[0,48]取我们需要的值[1,11]
            if (num >= 1 && num <= 40)
                return num % 10 + 1;

            // 对多出来的值0,41,42...48继续利用
            // num % 40 = 0, 1, 2, 3, ...,8 = rand9() - 1
            // 继续利用公式构造等概率分布
            num = (num % 40) * 7 + rand7() - 1;
            // num取值为[0, 62]
            // 无用数字只有0, 1, 2
            if (num >= 1 && num <= 60)
                return num % 10 + 1;

            // 同理
            // num % 60 = 0, 1, 2 = rand3() - 1
            // num取值为[0, 20]
            num = (num % 60) * 7 + rand7() - 1;
            if (num >= 1 && num <= 20)
                return num % 10 + 1;

            // 最后无用数字只有0
        }
    }
};
```



## 503 下一个更大元素II

https://leetcode.cn/problems/next-greater-element-ii/description/

遍历一遍数组显然不够，可复制一份数组拼接在原数组末尾，实现循环数组

实际操作中，我们不需要复制，只需要遍历两次数组即可，i % n 对应nums的下标

```C++
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        int n = nums.size();
        stack<int> stk;
        vector<int> ans(n, -1);
        for (int i = 0; i < 2 * n - 1; i++) {
            while (!stk.empty() && nums[i % n] > nums[stk.top()]) {
                ans[stk.top()] = nums[i % n];
                stk.pop();
            }
            stk.push(i % n);
        }
        return ans;
    }
};
```

**复杂度分析**

时间复杂度: O(n)，其中 n 是序列的长度。我们需要遍历该数组中每个元素最多 2 次，每个元素出栈与入栈的总次数也不超过 4 次。

空间复杂度: O(n)，其中 n 是序列的长度。空间复杂度主要取决于栈的大小，栈的大小至多为 2n−1。

## 509 斐波那契数

https://leetcode.cn/problems/fibonacci-number/description/

和爬楼梯类似，不赘述

## 547 省份数量

### 解法一：dfs

本题nxn矩阵可视作邻接矩阵，对其可以进行图论的dfs或bfs遍历，找出所有的连通分量

关键在于如果计算连通分量的个数：如果第i - 1次dfs遍历后仍有节点没有被遍历，说明其属于新的连通分量

```C++
class Solution {
private:
    void dfs(vector<vector<int>>& isConnected, vector<bool>& isVisited, int idx){
        for(int j = 0; j < isConnected[idx].size(); j++){
            if(isConnected[idx][j] && !isVisited[j]){
                isVisited[j] = true;
                dfs(isConnected, isVisited, j);
            }
        }
    }
public:
    int findCircleNum(vector<vector<int>>& isConnected) {
        int n = isConnected.size();
        vector<bool> isVisited(n, false);
        int ans = 0;
        for(int i = 0; i < n; i++){
            // 遍历一遍后，如果第二遍还出现了没有遍历过的节点
            // 说明出现新的连通分量
            if(!isVisited[i]){
                // 找出i对应的所有连通节点，对其标记
                dfs(isConnected, isVisited, i);
                // i没有被访问过，所以出现新的连通分量
                ans++;
            }
        }
        return ans;
    }
};
```

**复杂度分析**

时间复杂度：$O(n^2)$，其中 n 是城市的数量。需要遍历矩阵 n 中的每个元素。

空间复杂度：$O(n)$，其中 n 是城市的数量。需要使用数组 visited 记录每个城市是否被访问过，数组长度是 n，递归调用栈的深度不会超过 n

### 解法二： bfs

与dfs类似，以每个节点bfs遍历一遍，如果遍历停止，说明其访问完所有相连的节点

遍历后还有未访问的节点，说明有新的连通分量

```C++
class Solution {
public:
    int findCircleNum(vector<vector<int>>& isConnected) {
        int n = isConnected.size();
        vector<bool> isVisited(n, false);
        int ans = 0;
        for(int i = 0; i < n; i++){
            queue<int> q;
            if(!isVisited[i]){
                ans++;
                q.push(i);
                while(!q.empty()){
                    int idx = q.front();
                    q.pop();
                    for(int j = 0; j < n; j++){
                        if(isConnected[idx][j] && !isVisited[j]){
                            q.push(j);
                            isVisited[j] = true;
                        }
                    }
                }
            }
        }
        return ans;
    }
};
```

**复杂度分析**

时间复杂度：$O(n^2)$，其中 n 是城市的数量。需要遍历矩阵 isConnected 中的每个元素。

空间复杂度：$O(n)$，其中 n 是城市的数量。需要使用数组 visited 记录每个城市是否被访问过，数组长度是 n，广度优先搜索使用的队列的元素个数不会超过 n。

### 解法三： 并查集

```C++
class Solution {
public:
    int Find(vector<int>& p, int x) {
        if (p[x] != x)
            p[x] = Find(p, p[x]);
        return p[x];
    }
    void Union(vector<int>& p, int x, int y) { p[Find(p, x)] = Find(p, y); }

    int findCircleNum(vector<vector<int>>& isConnected) {
        int n = isConnected.size();
        int m = isConnected[0].size();
        vector<int> parent(n);
        for (int i = 0; i < n; i++)
            parent[i] = i;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < m; j++) {
                if (isConnected[i][j])
                    Union(parent, i, j);
            }
        }
        int ans = 0;
        for (int i = 0; i < n; i++) {
            if (parent[i] == i)
                ans++;
        }
        return ans;
    }
};
```

**复杂度分析**

时间复杂度：$O(n^2logn)$，其中 n 是城市的数量。需要遍历矩阵 isConnected 中的所有元素，时间复杂度是 $O(n^2)$，如果遇到相连关系，则需要进行 2 次查找和最多 1 次合并，一共需要进行 $2n^2$次查找和最多$n^2$次合并，因此总时间复杂度是$ O(2n^2logn^2 )=O(n^2logn)$。这里的并查集使用了路径压缩，但是没有使用按秩合并，最坏情况下的时间复杂度是 $O(n^2logn)$，平均情况下的时间复杂度依然是 $O(n2α(n))$，其中 α 为阿克曼函数的反函数，$α(n) $可以认为是一个很小的常数。

空间复杂度：$O(n)$，其中 n 是城市的数量。需要使用数组 parent 记录每个城市所属的连通分量的祖先。

## 560 和为k的子数组

```C++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        // key记录值sum[i] - k，value记录个数
        unordered_map<int,int> hashMap;
        int n = nums.size();
        int pre = 0;
        int count = 0;
        // 这一步不能省
        // 因为pre == k的时候一定有解
        // 从前缀和为0的时候开始
        hashMap[0] = 1;
        // sum[i] - sum[j - 1] = k
        // 即sum[j - 1] = sum[i] - k
        // 此处的pre就是sum[i]
        for(int i = 0; i < n; i++){
            pre += nums[i];
            auto it = hashMap.find(pre - k);
            if(it != hashMap.end())
                count += it->second;
            // 记录前缀和为pre的个数
            hashMap[pre]++;
        }
        return count;
    }
};
```



## 684 冗余连接

https://leetcode.cn/problems/redundant-connection/

本题给出n个点和n条边，由于树的边数为节点数 -1， 所以实际上只会多出一条边，这条边就是我们要求的解

如果多条答案边，创一个ans数组依次放入，最后返回末尾元素即可（即栈顶元素）

```C++
class Solution {
private:
    vector<int> p;
    void init(int n){
        p.resize(n + 1);
        for(int i = 1; i <= n; i++)
            p[i] = i;
    }
    int Find(int x){
        if(p[x] != x) p[x] = Find(p[x]);
        return p[x];
    }
    void Union(int x, int y){
        p[Find(x)] = Find(y);
    }
public:
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        int n = edges.size();
        init(n);
        for(auto edge: edges){
            int x = edge[0], y = edge[1];
            if(Find(x) != Find(y)){
                Union(x, y);
            }
            // p[x] == p[y]说明x和y在一棵树中了，此时多加的那条边会使树变成图，即题解
            else return edge;
        }
        return vector<int>();
    }
};
```



## 739 每日温度

https://leetcode.cn/problems/daily-temperatures/description/

> 找出左/右边第一个比栈顶元素小 ->  单调递增栈
>
> 找出左/右边第一个比栈顶元素大 -> 单调递减栈

```C++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        int n = temperatures.size();
        stack<int> stk;
        vector<int> ans(n, 0);
        for(int i = 0; i < n; i++){
          	// 单调递减栈，弹出说明新元素i是第一个比栈顶元素大的元素
            while(!stk.empty() && temperatures[i] > temperatures[stk.top()]){
              	// 题目问的是几天后
                ans[stk.top()] = i - stk.top();
                stk.pop();
            }
            stk.push(i);
        }
        return ans;
    }
};
```

**复杂度分析**

时间复杂度：O(n)，其中 n 是温度列表的长度。正向遍历温度列表一遍，对于温度列表中的每个下标，最多有一次进栈和出栈的操作。

空间复杂度：O(n)，其中 n 是温度列表的长度。需要维护一个单调栈存储温度列表中的下标。

## 763 划分字母区间

```C++
class Solution {
public:
    vector<int> partitionLabels(string s) {
        int n = s.size();
        int last[26];
        vector<int> res;
        // 统计每个字符最后出现的下标
        for(int i = 0; i < n; i++){
            last[s[i] - 'a'] = i;
        }
        // start为区间开始点，end为区间结束点
        // end一定为某区间内所有last[j]的最大值
        int start = 0, end = 0;
        for(int i = 0; i < n; i++){
            end = max(end, last[s[i] - 'a']);
            // i == end 说明区间终点end已确定 且已遍历到区间终点end
            if(i == end){
                res.push_back(end - start + 1);
                start = end + 1;
            }
        }
        return res;
    }
};
```

复杂度分析

时间复杂度：O(n)，其中 n 是字符串的长度。需要遍历字符串两次，第一次遍历时记录每个字母最后一次出现的下标位置，第二次遍历时进行字符串的划分。

空间复杂度：O(∣Σ∣)，其中 Σ 是字符串中的字符集。这道题中，字符串只包含小写字母，因此 ∣Σ∣=26。

## 912 排序数组

### 堆排序

```C++
class Solution {
private:
    void heapify(vector<int>& nums, int u, int heapSize) {
        int smallest = u;
        int l = 2 * u + 1, r = 2 * u + 2;
        if (l < heapSize && nums[l] < nums[smallest])
            smallest = l;
        if (r < heapSize && nums[r] < nums[smallest])
            smallest = r;
        if (smallest != u) {
            swap(nums[smallest], nums[u]);
            heapify(nums, smallest, heapSize);
        }
    }
    void buildMinHeap(vector<int>& nums, int heapSize) {
        for (int i = nums.size() / 2; i >= 0; i--)
            heapify(nums, i, heapSize);
    }

public:
    vector<int> sortArray(vector<int>& nums) {
        vector<int> ans;
        int heapSize = nums.size();
        buildMinHeap(nums, heapSize);
        for (int i = heapSize - 1; i >= 0; i--) {
            ans.push_back(nums[0]);
            swap(nums[0], nums[i]);
            heapify(nums, 0, i);
        }
        return ans;
    }
};
```



### 归并排序

```C++
class Solution {
private:
    vector<int> tmp;
    void mergeSort(vector<int>& nums,int l,int r){
        if(l < r){
            int mid = (l + r)>> 1;
            mergeSort(nums,l,mid);
            mergeSort(nums,mid+ 1,r);
            int cnt = 0;
            int i = l, j = mid + 1;
            while(i <= mid && j <= r){
                if(nums[i] <= nums[j])
                    tmp[cnt++] = nums[i++];
                else
                    tmp[cnt++] = nums[j++];
            }
            while(i <= mid)
                tmp[cnt++] = nums[i++];
            while(j <= r)
                tmp[cnt++] = nums[j++];
            for(int i = 0; i < r - l + 1;i++)
                nums[l+i] = tmp[i];
        }
    }
public:
    vector<int> sortArray(vector<int>& nums) {
        tmp.resize(nums.size(),0);
        mergeSort(nums,0,nums.size() - 1);
        return nums;
    }
};
```



### 快速排序

```C++
class Solution {
private:
    void quickSort(vector<int>& nums, int l, int r) {
        // 注意是l < r
        if (l < r) {
            int idx = l + rand() % (r - l + 1);
            int pivot = nums[idx];
            int i = l - 1, j = r + 1;
            while (i < j) {
                do
                    i++;
                while (nums[i] < pivot);
                do
                    j--;
                while (nums[j] > pivot);
                if(i < j)
                    swap(nums[i],nums[j]);
            }
            // 因为是 l < r 所以这里是quickSort(nums, l, j)而不是quickSort(nums, l, j - 1)
            quickSort(nums, l, j);
            quickSort(nums, j + 1, r);
        }
    }

public:
    vector<int> sortArray(vector<int>& nums) {
        quickSort(nums, 0, nums.size() - 1);
        return nums;
    }
};
```



## 刷题总结

### 滑动窗口

> 单向队列queue，`#include <queue>` , 仅支持插入队尾push(int i)，弹出队头pop()
>
> 双向队列duque，`#include<deque>`, 支持插入队头push_front(int i)，插入队尾push_back(int i)，删除队头pop_front()，删除队尾pop_back()

对于一般的滑动窗口，如果不用pop队尾，可以用queue

```C++
		queue<int> q;
		// 遍历序列每个元素
		for(int i = 0; i < n; i++){
      	// 检查新加入的元素是否满足条件，如果满足，加入队尾
      	if(check(q.front(), i))
      			q.push(i);
      	// 根据问题，对插入的nums[i]操作
      	...
      	// check(q.front())意为：检查队头是否满足条件，如果不满足，则弹出
				while(!q.empty() && check(q.front())){
						q.pop();
				}
		}
```

如果需要求滑动窗口最大最小值，可以使用deque

```C++
		deque<int> q;
		// 遍历序列每个元素
		for(int i = 0; i < n; i++){
      	// 检查队头是否满足条件，如果不满足，弹出队头	
      	while(!q.empty() && check(q.front()))	q.pop_front();
      	// 检查队尾是否满足条件，如果不满足，弹出队尾
      	while(!q.empty() && check(q.back(), i)) q.pop_back(i);
      	// 将新元素加入队尾
      	q.push_back(i);
		}
```

例如acwing 154 滑动窗口

https://www.acwing.com/problem/content/156/

```C++
#include <iostream>
#include <deque>
using namespace std;

const int N = 1e6 + 10;
int a[N];
int n, k;

int main(){
    deque<int> q;
    cin >> n >> k;
    for(int i = 0; i < n; i++)
        cin >> a[i];
    // 求滑动窗口最小值
    for(int i = 0; i < n; i++){
        // 队列存储的是滑动窗口的最小值的索引
        // 队列的长度不一定为k
        
        // 窗口第一个元素的索引为idx，长度为k的窗口对应的末尾元素的索引为idx + k - 1，也为i
        // 所以i = idx + k - 1 或idx = i - k + 1
        // 可推出：
        // （1）i = idx + k - 1:
        //      所以一开始idx = 0，窗口未满，往里面填元素，当i == k - 1时，窗口刚好满；之后i > k - 1
        // （2）idx = i + k - 1:
        //      想要保持窗口一直为k，那么需要idx = i - k + 1 <= q.front()
        //      如果i - k + 1 > q.front() 说明队头已经不在窗口内，需要弹出
        
        
        // pop前保证队列不为空 且 保证窗口始终保持在k个
        // 如果超出k个，说明队头需要弹出
        // 如果不足或等于k个，队头也不需要弹出
        while(!q.empty() && i - k + 1 > q.front()) q.pop_front();
        // pop前保证队列不为空 且 如果队尾元素a[q.back()]比新元素a[i]还小
        // 那么只要a[i]还在窗口内，之后窗口的最小值一定不为a[q.back()]
        // 因此弹出队尾元素
        while(!q.empty() && a[i] < a[q.back()]) q.pop_back();
        q.push_back(i);
        
        if(i - k + 1 >= 0)
            cout << a[q.front()] << " ";
    }
    q.clear();
    cout << endl;
    // 求滑动窗口最大值
    for(int i = 0; i < n; i++){
        while(!q.empty() && i - q.front() + 1 > k) q.pop_front();
        while(!q.empty() && a[i] > a[q.back()]) q.pop_back();
        q.push_back(i);
        if(i - k + 1 >= 0)
            cout << a[q.front()] << " ";
    }
}
```

当然，还可以使用双指针，只需要指向原来的数组即可

### 图：网格问题

网格问题

### 并查集

可解决图的连通分量问题

### 单调栈

## STL

### 二分查找

https://blog.csdn.net/weixin_45031801/article/details/137544229

lower_bound( begin , end , val , less<type>() )
上述代码中加入了 less<type>() 自定义比较函数：适用于从小到大排序的有序序列，从数组/容器的 beign 位置起，到 end-1 位置结束，查找第一个 大于等于 val 的数字
lower_bound( begin , end , val , greater<type>() )
上述代码中加入了 greater<type>() 自定义比较函数：适用于从大到小排序的有序序列，从数组/容器的 beign 位置起，到 end-1 位置结束，查找第一个 小于等于 val 的数字

#### upper_bound

前提是有序的情况下，upper_bound返回第一个大于val值的位置。（通过二分查找）

#### lower_bound

前提是**有序**的情况下，lower_bound 返回指向第一个值不小于 val 的位置，也就是返回第一个大于等于val值的位置。（通过二分查找）

***\*注意：\****需要注意的是如果例子中（val >= 8）,那么迭代器就会指向last位置，***\*也就是数组尾元素的下一个，不管val多大，迭代器永远指向尾元素的下一个位置\****

```c++
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;
 
int main()
{
	vector<int> v = { 3,4,1,2,8 };
	// 先排序
	sort(v.begin(), v.end());  // 1 2 3 4 8
 
	// 定义两个迭代器变量
	vector<int>::iterator iter1;
	vector<int>::iterator iter2;
 
	// 在动态数组中寻找 >=3 出现的第一个数 并以迭代器的形式返回
	iter1 = lower_bound(v.begin(), v.end(), 3);  // -- 指向3
	// 在动态数组中寻找 >=7 出现的第一个数 并以迭代器的形式返回
	iter2 = lower_bound(v.begin(), v.end(), 7);  // -- 指向8
 
	cout << distance(v.begin(), iter1) << endl; //下标 2
	cout << distance(v.begin(), iter2) << endl; //下标 4 
	return 0;
}

```

