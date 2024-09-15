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

