

# LeetCode刷题笔记

[TOC]

--------------------

## 简单

#### [1122. 数组的相对排序](https://leetcode-cn.com/problems/relative-sort-array/)

**难度：**简单

给你两个数组，`arr1` 和 `arr2`，

- `arr2` 中的元素各不相同
- `arr2` 中的每个元素都出现在 `arr1` 中

对 `arr1` 中的元素进行排序，使 `arr1` 中项的相对顺序和 `arr2` 中的相对顺序相同。未在 `arr2` 中出现过的元素需要按照升序放在 `arr1` 的末尾。

 

**示例：**

```
输入：arr1 = [2,3,1,3,2,4,6,7,9,2,19], arr2 = [2,1,4,3,9,6]
输出：[2,2,2,1,4,3,3,9,6,7,19]

```

 

**提示：**

- `1 <= arr1.length, arr2.length <= 1000`
- `0 <= arr1[i], arr2[i] <= 1000`
- `arr2` 中的元素 `arr2[i]` 各不相同
- `arr2` 中的每个元素 `arr2[i]` 都出现在 `arr1` 中**实现：**

```
int cmp(const void* p1, const void* p2) {
    return *(int*)p1 - *(int*)p2;
}

int* relativeSortArray(int* arr1, int arr1Size, int* arr2, int arr2Size, int* returnSize){
    if (arr1 == NULL || arr1Size == 0 || arr2 == NULL || arr2Size == 0) {
        *returnSize = 0;
        return NULL;
    }
    int arr1Index = 0;
    qsort(arr1 + arr1Index, arr1Size - arr1Index, sizeof(int), cmp);
    for (int arr2Index = 0; arr2Index < arr2Size; ) {
        int *p = NULL;
        for (int j = arr1Index; j < arr1Size; j++) {
            if (arr1[j] == arr2[arr2Index]) {
                p = &arr1[j];
            }
        }
        if (p == NULL) {
            arr2Index++;
            continue;
        }
        int tmp = *p;
        while (p - (arr1 + arr1Index) > 0) {
            *p = *(p - 1);
            p--;
        }
        *p = tmp;
        arr1Index++;
    }
    *returnSize = arr1Size;
    return arr1;
}
```

------

#### [53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

**难度：**简单

给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

 

**示例 1：**

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

**示例 2：**

```
输入：nums = [1]
输出：1
```

**示例 3：**

```
输入：nums = [0]
输出：0
```

**示例 4：**

```
输入：nums = [-1]
输出：-1
```

**示例 5：**

```
输入：nums = [-100000]
输出：-100000
```

 

**提示：**

- `1 <= nums.length <= 3 * 104`
- `-105 <= nums[i] <= 105`

 

**思路：（动态规划）**
建立数组`dp`,`dp[i]`表示第`i`个元素被选中参与构建最大子序中所能获得的最大的和。
`dp[i]`有两种可能：
+ 与前一个元素一起参与构建最大子序列，此时`dp[i] = dp[i-1] + nums[i]`
+ 自己作为最大子序列的起始位置，此时`dp[i] = nums[i]`

因此，转移方程为：
`dp[i] = max(dp[i-1]+nums[i], nums[i])`

最大子序和即为数组`dp`中的最大元素。

**实现：**
```
int myMax(int a, int b) {
    return a > b ? a : b;
}

int maxSubArray(int* nums, int numsSize){
    if (nums == NULL || numsSize == 0) {
        return 0;
    }

    if (numsSize < 2) {
        return nums[0];
    }

    int *dp = (int *)malloc(sizeof(int) * numsSize);
    dp[0] = nums[0];
    for (int i = 1; i <numsSize; i++) {
        dp[i] = myMax(dp[i - 1] + nums[i], nums[i]);
        }

    int max = dp[0];
    for (int i = 0; i < numsSize; i++) {
        max = myMax(max, dp[i]);
    }
    free(dp);
    return max;
}
```

----------------

#### [392. 判断子序列](https://leetcode-cn.com/problems/is-subsequence/)

**难度：**简单

给定字符串 **s** 和 **t** ，判断 **s** 是否为 **t** 的子序列。

字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，`"ace"`是`"abcde"`的一个子序列，而`"aec"`不是）。

**进阶：**

如果有大量输入的 S，称作 S1, S2, ... , Sk 其中 k >= 10亿，你需要依次检查它们是否为 T 的子序列。在这种情况下，你会怎样改变代码？

**致谢：**

特别感谢 [@pbrother ](https://leetcode.com/pbrother/)添加此问题并且创建所有测试用例。

 

**示例 1：**

```
输入：s = "abc", t = "ahbgdc"
输出：true
```

**示例 2：**

```
输入：s = "axc", t = "ahbgdc"
输出：false
```

 

**提示：**

- `0 <= s.length <= 100`
- `0 <= t.length <= 10^4`
- 两个字符串都只由小写字符组成。

**思路：**
指针`s`和`t`是指向两个字符串的指针。若二者指向的字符相同，即：`*s==*t`。则二者同时自增1，否则只将t自增1。
如果`s`能遍历完所指向的字符串（即最后`*s='\0'`），则`s`是`t`的子序列，否则不是。

**实现：**
```
bool isSubsequence(char * s, char * t){
    if (s == NULL || t == NULL) {
        return false;
    }
    while (*s != '\0' && *t != '\0') {
        if (*s == *t) {
            s++;
        }
        t++;
    }
    if (*s == '\0') {
        return true;
    } else {
        return false;
    }
}
```

----------------

#### [122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

**难度：**简单

给定一个数组，它的第 *i* 个元素是一支给定股票第 *i* 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

**注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

 

**示例 1:**

```
输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
```

**示例 2:**

```
输入: [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
```

**示例 3:**

```
输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```

 

**提示：**

- `1 <= prices.length <= 3 * 10 ^ 4`
- `0 <= prices[i] <= 10 ^ 4`



**思路（动态规划）：**
建立数组`bef`，`bef[i]`表示第i天能获得的最大利润。有两种情况：
+ `prices[i] > prices[i -1`时，`bef[i]=bef[i-1]+prices[i]-prices[i-1]`
+ `prices[i]<=prices[i-1]时`，`bef[i]=bef[i-1]`

**实现：**
```
int maxProfit(int* prices, int pricesSize){
    if (prices == NULL || pricesSize < 2) {
        return 0;
    }
    int *bef = (int *)malloc(sizeof(int) * pricesSize);
    bef[0] = 0;
    for (int i = 1; i < pricesSize; i++) {
        if (prices[i] > prices[i-1]) {
            bef[i] = bef[i-1] + prices[i] - prices[i-1];
        } else {
            bef[i] = bef[i-1];
        }
    }
    int ret = bef[pricesSize-1];
    free(bef);
    return ret;
}
```

----------------

#### [455. 分发饼干](https://leetcode-cn.com/problems/assign-cookies/)

**难度：**简单

假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。

对每个孩子 `i`，都有一个胃口值 `g[i]`，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 `j`，都有一个尺寸 `s[j]` 。如果 `s[j] >= g[i]`，我们可以将这个饼干 `j` 分配给孩子 `i` ，这个孩子会得到满足。你的目标是尽可能满足越多数量的孩子，并输出这个最大数值。

**示例 1:**

```
输入: g = [1,2,3], s = [1,1]
输出: 1
解释: 
你有三个孩子和两块小饼干，3个孩子的胃口值分别是：1,2,3。
虽然你有两块小饼干，由于他们的尺寸都是1，你只能让胃口值是1的孩子满足。
所以你应该输出1。
```

**示例 2:**

```
输入: g = [1,2], s = [1,2,3]
输出: 2
解释: 
你有两个孩子和三块小饼干，2个孩子的胃口值分别是1,2。
你拥有的饼干数量和尺寸都足以让所有孩子满足。
所以你应该输出2.
```

 

**提示：**

- `1 <= g.length <= 3 * 104`

- `0 <= s.length <= 3 * 104`

- `1 <= g[i], s[j] <= 231 - 1`

  

**思路：（优先级队列）**
将孩子的胃口值数组`g`和饼干尺寸数组`s`升序排列。如果`g[gHead] < s[sHead]`，则说明最小尺寸饼干足以满足最小胃口值，二者同时出队列；否则最小尺寸的饼干出队列（因为这块饼干无用，不足以满足现有队列中任意一个孩子的胃口）。胃口值队列出列的次数，即为能满足的最大孩子数。

**实现：**
```
int cmp(const void* a, const void* b) {
    return *(int *)a - *(int *)b;
}

int findContentChildren(int* g, int gSize, int* s, int sSize){
    if (g == NULL || gSize == 0 || sSize == NULL || sSize == 0) {
        return 0;
    }
    qsort(g, gSize, sizeof(int), cmp);
    qsort(s, sSize, sizeof(int), cmp);

    int gHead, sHead;
    gHead = sHead = 0;
    while (gHead < gSize && sHead < sSize) {
        if (g[gHead] <= s[sHead]) {
            gHead++;
        }
        sHead++;
    }

    return gHead;
}
```

----------------

#### [111. 二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

**难度：**简单

给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

**说明：**叶子节点是指没有子节点的节点。

 

**示例 1：**

![](https://assets.leetcode.com/uploads/2020/10/12/ex_depth.jpg)

```
输入：root = [3,9,20,null,null,15,7]
输出：2
```

**示例 2：**

```
输入：root = [2,null,3,null,4,null,5,null,6]
输出：5
```

 

**提示：**

- 树中节点数的范围在 `[0, 105]` 内
- `-1000 <= Node.val <= 1000`

**思路：（递归）**
递归求取左子树（如果存在）和右子树（如果存在）的最小深度。

**实现：**
```
int minDepthCore(struct TreeNode* root, int curDepth) {
    if (root->left == NULL && root->right == NULL) {
        return curDepth;
    }
    if (root->left != NULL && root->right != NULL) {
        int minLeftDepth = minDepthCore(root->left, curDepth + 1);
        int minRightDepth = minDepthCore(root->right, curDepth + 1);
        return minLeftDepth < minRightDepth ? minLeftDepth : minRightDepth;
    }
    if (root->left != NULL) {
        return minDepthCore(root->left, curDepth + 1);
    }
    return minDepthCore(root->right, curDepth + 1);
}
int minDepth(struct TreeNode* root){
    if (root == NULL) {
        return 0;
    }
    return minDepthCore(root, 1);
}
```

以上代码借助函数调用时的栈实现的，也可自己模拟栈实现：
```
#define MAX_NODE_NUM 256
int minDepth(struct TreeNode* root){
    if (root == NULL) {
        return 0;
    }
    // return minDepthCore(root, 1);
    struct TreeNode *stackKey[MAX_NODE_NUM] = {NULL};
    int *stackValue[MAX_NODE_NUM];
    int stackTop = 0;
    stackKey[stackTop] = root;
    stackValue[stackTop++] = 1;
    int depth = 1, min = INT_MAX;
    while (stackTop > 0) {
        struct TreeNode *node = stackKey[stackTop - 1];
        int depth = stackValue[--stackTop];
        if (node->left == NULL && node->right==NULL) {
            min = min < depth ? min : depth;
        } else {
            if (node->left != NULL) {
                stackKey[stackTop] = node->left;
                stackValue[stackTop++] = depth + 1;
            }
            if (node->right != NULL) {
                stackKey[stackTop] = node->right;
                stackValue[stackTop++] = depth + 1;
            }
        }
    }
    return min;
}
```

----------------

#### [100. 相同的树](https://leetcode-cn.com/problems/same-tree/)

**难度：**简单

给你两棵二叉树的根节点 `p` 和 `q` ，编写一个函数来检验这两棵树是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

 

**示例 1：**

![](https://assets.leetcode.com/uploads/2020/12/20/ex1.jpg)

```
输入：p = [1,2,3], q = [1,2,3]
输出：true
```

**示例 2：**

![](https://assets.leetcode.com/uploads/2020/12/20/ex2.jpg)

```
输入：p = [1,2], q = [1,null,2]
输出：false
```

**示例 3：**

![](https://assets.leetcode.com/uploads/2020/12/20/ex3.jpg)

```
输入：p = [1,2,1], q = [1,1,2]
输出：false
```

 

**提示：**

- 两棵树上的节点数目都在范围 `[0, 100]` 内
- `-104 <= Node.val <= 104`

**思路：**
在两棵树中同时遍历：如果两个节点同为NULL，认为这两个节点等同；否则，如果这两个节点的值相等且左右子树相同，也认为这两个节点等同；否则，认为这两个节点不等同，树不相同。

**实现：**
```
bool isSameTree(struct TreeNode* p, struct TreeNode* q){
    if (p == NULL && q == NULL) {
        return true;
    }
    if (p != NULL && q != NULL) {
        return (p->val == q->val) && isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    } else {
        return false;
    }
}
```

----------------

#### [35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)

**难度：**简单

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

**示例 1:**

```
输入: [1,3,5,6], 5
输出: 2
```

**示例 2:**

```
输入: [1,3,5,6], 2
输出: 1

```

**示例 3:**

```
输入: [1,3,5,6], 7
输出: 4

```

**示例 4:**

```
输入: [1,3,5,6], 0
输出: 0
```

**思路：（二分查找）**

**实现：**

```
int searchInsert(int* nums, int numsSize, int target){
    if (nums == NULL || numsSize == 0) {
        return 0;
    }
    if (target < nums[0]) {
        return 0;
    }
    if (target > nums[numsSize - 1]) {
        return numsSize;
    }

    int beginIndex = 0;
    int endIndex = numsSize - 1;
    int midIndex;
    bool found = false;
    int ret = 0;
    while (beginIndex < endIndex) {
        if (nums[beginIndex] == target) {
            found = true;
            ret =  beginIndex;
            break;
        }
        if (nums[endIndex] == target) {
            found = true;
            ret = endIndex;
            break;
        }
        if (endIndex - beginIndex == 1) {
            ret = endIndex;
            break;
        }
        midIndex = (beginIndex + endIndex) / 2;
        if (nums[midIndex] > target) {
            endIndex = midIndex;
        } else {
            beginIndex = midIndex;
        }
    }
    return ret;
}
```

------

#### [169. 多数元素](https://leetcode-cn.com/problems/majority-element/)

**难度：**简单

给定一个大小为 *n* 的数组，找到其中的多数元素。多数元素是指在数组中出现次数 **大于** `⌊ n/2 ⌋` 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

 

**示例 1：**

```
输入：[3,2,3]
输出：3

```

**示例 2：**

```
输入：[2,2,1,1,1,2,2]
输出：2
```

**思路：**
排序，取数组中间位置的元素。

**实现：**

```
int cmp(const void* p1, const void* p2)
{
    return *(int*)p1 - *(int*)p2;
}

int majorityElement(int* nums, int numsSize){
    qsort(nums, numsSize, sizeof(int), cmp);
    return nums[numsSize/2];
}
```

------

#### [1137. 第 N 个泰波那契数](https://leetcode-cn.com/problems/n-th-tribonacci-number/)

**难度：**简单

泰波那契序列 Tn 定义如下： 

T0 = 0, T1 = 1, T2 = 1, 且在 n >= 0 的条件下 Tn+3 = Tn + Tn+1 + Tn+2

给你整数 `n`，请返回第 n 个泰波那契数 Tn 的值。

 

**示例 1：**

```
输入：n = 4
输出：4
解释：
T_3 = 0 + 1 + 1 = 2
T_4 = 1 + 1 + 2 = 4

```

**示例 2：**

```
输入：n = 25
输出：1389537

```

 

**提示：**

- `0 <= n <= 37`
- 答案保证是一个 32 位整数，即 `answer <= 2^31 - 1`。

**思路：（动态规划/记忆递归）**
类似于斐波那契数列。为加快运行速度，使用动态规划从前往后计算。

**实现：**

```
int tribonacci(int n){
    if (n == 0) {
        return 0;
    }
    if (n <= 2) {
        return 1;
    }
    int *dp = (int *)malloc(sizeof(int) * (n + 1));
    dp[0] = 0;
    dp[1] = dp[2] = 1;
    for (int i = 3; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2] + dp[i - 3];
    }
    int ret = dp[n];
    free(dp);
    return ret;
}
```

------

#### [58. 最后一个单词的长度](https://leetcode-cn.com/problems/length-of-last-word/)

**难度：**简单

给你一个字符串 `s`，由若干单词组成，单词之间用空格隔开。返回字符串中最后一个单词的长度。如果不存在最后一个单词，请返回 0 。

**单词** 是指仅由字母组成、不包含任何空格字符的最大子字符串。

 

**示例 1：**

```
输入：s = "Hello World"
输出：5

```

**示例 2：**

```
输入：s = " "
输出：0

```

 

**提示：**

- `1 <= s.length <= 104`
- `s` 仅有英文字母和空格 `' '` 组成

**思路：**
从后往前找，一开始遇到的空格跳过，遇到字符开始计数，遇到第一个空格或者遍历到字符串头则结束。

**实现：**

```
int lengthOfLastWord(char * s){
    if (s == NULL) {
        return 0;
    }
    int i = strlen(s) - 1;
    while (i >= 0 && s[i] == ' ') {
        i--;
    }
    int lastLen = 0;
    while (i >= 0 && s[i] != ' ') {
        lastLen++;
        i--;
    }
    return lastLen;
}
```

------

## [933. 最近的请求次数](https://leetcode-cn.com/problems/number-of-recent-calls/)

**难度：**简单

写一个 `RecentCounter` 类来计算特定时间范围内最近的请求。

请你实现 `RecentCounter` 类：

- `RecentCounter()` 初始化计数器，请求数为 0 。
- `int ping(int t)` 在时间 `t` 添加一个新请求，其中 `t` 表示以毫秒为单位的某个时间，并返回过去 `3000` 毫秒内发生的所有请求数（包括新请求）。确切地说，返回在 `[t-3000, t]` 内发生的请求数。

**保证** 每次对 `ping` 的调用都使用比之前更大的 `t` 值。

 

**示例：**

```
输入：
["RecentCounter", "ping", "ping", "ping", "ping"]
[[], [1], [100], [3001], [3002]]
输出：
[null, 1, 2, 3, 3]

解释：
RecentCounter recentCounter = new RecentCounter();
recentCounter.ping(1);     // requests = [1]，范围是 [-2999,1]，返回 1
recentCounter.ping(100);   // requests = [1, 100]，范围是 [-2900,100]，返回 2
recentCounter.ping(3001);  // requests = [1, 100, 3001]，范围是 [1,3001]，返回 3
recentCounter.ping(3002);  // requests = [1, 100, 3001, 3002]，范围是 [2,3002]，返回 3
```

 

**提示：**

- `1 <= t <= 109`
- 保证每次对 `ping` 调用所使用的 `t` 值都 **严格递增**
- 至多调用 `ping` 方法 `104` 次
  **思路：（队列）**
  构建队列，每次来一个ping，将ping的时刻放进队列的最前方，并剔除栈中距离此时刻大于3000ms的元素。所需要的返回值就是队列的长度。

**实现：**
```
typedef struct {
    int *queue;
    int tail;
} RecentCounter;

#define MAX_QUEUE_LEN 10001

RecentCounter* recentCounterCreate() {
    RecentCounter *rc = (RecentCounter *)malloc(sizeof(RecentCounter));
    rc->queue = (int *) malloc(sizeof(int) * MAX_QUEUE_LEN);
    memset(rc->queue, 0, sizeof(int) * MAX_QUEUE_LEN);
    rc->tail = 0;
    return rc;
}

int cmp(const void* p1, const void* p2) {
    int *pi1 = (int*)p1;
    int *pi2 = (int*)p2;
    return (*pi2 - *pi1);
}

int recentCounterPing(RecentCounter* obj, int t) {
    obj->queue[obj->tail] = t;
    (obj->tail)++;
    qsort(obj->queue, obj->tail, sizeof(int), cmp);
    for (int i = 0; i < obj->tail; i++) {
        if (t - obj->queue[i] > 3000) {
            obj->tail = i;
            break;
        }
    }
    return obj->tail;
}

void recentCounterFree(RecentCounter* obj) {
    free(obj->queue);
    obj->queue = NULL;
    free(obj);
    obj = NULL;
}
```

## 中等

#### [198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)

**难度：**中等

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你 **不触动警报装置的情况下** ，一夜之内能够偷窃到的最高金额。

 

**示例 1：**

```
输入：[1,2,3,1]
输出：4
解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
```

**示例 2：**

```
输入：[2,7,9,3,1]
输出：12
解释：偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。
```

 

**提示：**

- `0 <= nums.length <= 100`
- `0 <= nums[i] <= 400`

**思路：（动态规划）**
建立数组`benifit`，元素`benefit[i]`表示小偷偷到第i家房屋能获得的最大收益。由于需要间隔着偷，有两种选择：

- 偷第`i`家，则收益为`benifit[i - 2] + money[i]`
- 不偷第`i`家，则收益为`benifit[i - 1]`

为窃取最多金额，取二者最大。因此动态规划的转移方程为：
`benifit[i] = max(benifit[i-2] + money[i], benifit[i-1])`

**实现：**

```
int rob(int* nums, int numsSize){
    if (nums == NULL || numsSize == 0) {
        return 0;
    }
    if (numsSize < 2) {
        return nums[0];
    }

    int *dp = (int *)malloc(sizeof(int) * numsSize);
    dp[0] = nums[0];
    dp[1] = nums[0] > nums[1] ? nums[0] : nums[1];
    for (int i = 2; i < numsSize; i++) {
        int choose = dp[i - 2] + nums[i];
        int unchoose = dp[i - 1];
        dp[i] = choose > unchoose ? choose : unchoose;
    }
    int ret = dp[numsSize - 1];
    free(dp);
    return ret;
}
```

------

#### [102. 二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

**难度：**中等

给你一个二叉树，请你返回其按 **层序遍历** 得到的节点值。 （即逐层地，从左到右访问所有节点）。

 

**示例：**
二叉树：`[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

返回其层序遍历结果：

```
[
  [3],
  [9,20],
  [15,7]
]
```

**思路：（队列）**
从根节点开始遍历二叉树，并将遍历到的每个节点的左右子节点（如果存在）也放到队列中。遍历的同时记录下一层的起始节点。

**实现：**
```
#define MAX_NODES_NUM 1024

int** levelOrder(struct TreeNode* root, int* returnSize, int** returnColumnSizes){
    if (root == NULL) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }

    int **ret = (int**)malloc(sizeof(int*) * MAX_NODES_NUM);
    int retIndex = -1;
    int *retColSizes = (int*)malloc(sizeof(int) * MAX_NODES_NUM);
    int retColIndex = 0;
    struct TreeNode *queue[MAX_NODES_NUM]; // = (struct TreeNode**)malloc(sizeof(struct TreeNode*) * MAX_NODES_NUM);
    int front = 0;
    int back = 0;
    int nextLayerStartIndex = 0;
    bool hasSetNextLayerStartIndex = true;
    queue[back++] = root;
    while (front < back) {
        struct TreeNode *node = queue[front];
        if (front == nextLayerStartIndex) {
            hasSetNextLayerStartIndex = false;
            ret[++retIndex] = malloc(sizeof(int) * MAX_NODES_NUM);
            retColIndex = 0;
        }
        ret[retIndex][retColIndex++] = node->val;
        retColSizes[retIndex] = retColIndex;
        if (node->left != NULL) {
            queue[back] = node->left;
            if (!hasSetNextLayerStartIndex) {
                hasSetNextLayerStartIndex = true;
                nextLayerStartIndex = back;
            }
            back++;
        }
        if (node->right != NULL) {
            queue[back] = node->right;
            if (!hasSetNextLayerStartIndex) {
                hasSetNextLayerStartIndex = true;
                nextLayerStartIndex = back;
            }
            back++;
        }
        front++;
    }
    // free(queue);
    *returnSize = retIndex + 1;
    *returnColumnSizes = retColSizes;
    return ret;
}
```

----------------

#### [55. 跳跃游戏](https://leetcode-cn.com/problems/jump-game/)

**难度：**中等

给定一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标。

 

**示例 1：**

```
输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
```

**示例 2：**

```
输入：nums = [3,2,1,0,4]
输出：false
解释：无论怎样，总会到达下标为 3 的位置。但该下标的最大跳跃长度是 0 ， 所以永远不可能到达最后一个下标。
```

 

**提示：**

- `1 <= nums.length <= 3 * 104`
- `0 <= nums[i] <= 105`

**思路：（动态规划）**
建立数组`reach`，`reach[i]`记录位置`i`是否可达。从`nums[i-1]`往前遍历：对于前面的某个位置`j`, 如果`j`是可达的，且从`j`跳跃可以到达的位置大于`i`，则位置`i`是可达的。

**实现：**
```
bool canJump(int* nums, int numsSize){
    if (nums == NULL || numsSize == 0) {
        return true;
    }
    bool *reach = (bool*)malloc(sizeof(bool) * numsSize);
    memset(reach, false, sizeof(bool) * numsSize);
    reach[0] = true;
    for (int i = 1; i < numsSize; i++) {
        bool flag = false;
        for (int j = i - 1; j >= 0; j--) {
            if (nums[j] + j >= i && reach[j]) {
                flag = true;
                break;
            }
        }
        reach[i] = flag;
    }
    bool ret = reach[numsSize - 1];
    free(reach);
    return ret;
}
```

----------------

#### [91. 解码方法](https://leetcode-cn.com/problems/decode-ways/)

**难度：**中等

一条包含字母 `A-Z` 的消息通过以下映射进行了 **编码** ：

```
'A' -> 1
'B' -> 2
...
'Z' -> 26
```

要 **解码** 已编码的消息，所有数字必须基于上述映射的方法，反向映射回字母（可能有多种方法）。例如，`"111"` 可以将 `"1"` 中的每个 `"1"` 映射为 `"A"` ，从而得到 `"AAA"` ，或者可以将 `"11"` 和 `"1"`（分别为 `"K"` 和 `"A"` ）映射为 `"KA"` 。注意，`"06"` 不能映射为 `"F"` ，因为 `"6"` 和 `"06"` 不同。

给你一个只含数字的 **非空** 字符串 `num` ，请计算并返回 **解码** 方法的 **总数** 。

题目数据保证答案肯定是一个 **32 位** 的整数。

 

**示例 1：**

```
输入：s = "12"
输出：2
解释：它可以解码为 "AB"（1 2）或者 "L"（12）。
```

**示例 2：**

```
输入：s = "226"
输出：3
解释：它可以解码为 "BZ" (2 26), "VF" (22 6), 或者 "BBF" (2 2 6) 。
```

**示例 3：**

```
输入：s = "0"
输出：0
解释：没有字符映射到以 0 开头的数字。含有 0 的有效映射是 'J' -> "10" 和 'T'-> "20" 。由于没有字符，因此没有有效的方法对此进行解码，因为所有数字都需要映射。
```

**示例 4：**

```
输入：s = "06"
输出：0
解释："06" 不能映射到 "F" ，因为字符串开头的 0 无法指向一个有效的字符。 
```

 

**提示：**

- `1 <= s.length <= 100`
- `s` 只包含数字，并且可能包含前导零。

**思路一：(DFS)**

**实现：**
```
int numDecodingsCore(char *s, int curNumOfWays) {
    if (*s == '\0') {
        curNumOfWays = curNumOfWays + 1;
        return curNumOfWays;
    }
    if (*s == '0') {
        return curNumOfWays;
    }
    curNumOfWays = numDecodingsCore(s + 1, curNumOfWays);
    if (*(s + 1) != '\0' &&
        (*s == '1' || (*s == '2' && *(s + 1) <= '6'))) {
        curNumOfWays = numDecodingsCore(s + 2, curNumOfWays);
    }
    return curNumOfWays;
}

int numDecodings(char * s){
    int ret = numDecodingsCore(s, 0);
    return ret;
}
```


以上代码运行速度太慢，可以优化。

**思路二：(DP)**
建立数组`dp`，`dp[i]`表示到索引`i`字符串的解析方式的数目。
对于`s[i]`：
+ 如果`s[i-1]`与`s[i]`不构成一个可解码的数字(10-26)，则只能单独解析，即：`dp[i] = dp[i-1]`
+ 如果`s[i-1]`与`s[i]`可以构成一个可解码的数字(10-26)，则既可单独解析，也可一起解析：`dp[i] = dp[i-1] + dp[i-2]`
+ 如果`s[i-1]`无法单独解析，且与`s[i-1]`也无法一起解析，则`dp[i] = 0`

**实现：**
```
int numDecodings(char * s){
    if (s[0] == '0') {
        return 0;
    }
    int len = strlen(s);
    if (len == 1) {
        return 1;
    }
    int *dp = (int *)malloc(sizeof(int) * (len + 1));
    memset(dp, 0, sizeof(int) * (len + 1));
    dp[0] = 1;
    dp[1] = 1;
    for (int i = 2; i <= len; i++) {
        int j = i - 1;
        if (s[j] == '0' && (s[j - 1] != '1' && s[j - 1] != '2')) {  // 与前一字符组成00,30-90这种无法解析
            dp[i] = 0;
            break;
        } else if (s[j] == '0' && (s[j - 1] == '1' || s[j - 1] == '2')) {   // 与前一字符组成10,20，只能一起解析
            dp[i] = dp[i - 2];
        } else if (s[j - 1] == '1') {   // 与前一字符组成11-19，既可与前一字符一起解析，也可单独解析
            dp[i] = dp[i - 1] + dp[i - 2];
        } else if (s[j - 1] == '2' && s[j] <= '6') {    // 与前一字符组成21-25，既可与前一字符一起解析，也可单独解析
            dp[i] = dp[i - 1] + dp[i - 2];
        } else {    // 与前一字符组成27-99，只能单个解析
            dp[i] = dp[i - 1];
        }
    }
    int ret = dp[len];
    free(dp);
    return ret;
}
```
----------------

#### [62. 不同路径](https://leetcode-cn.com/problems/unique-paths/)

**难度：**中等

一个机器人位于一个 `m x n` 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？

 

**示例 1：**

![](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)

```
输入：m = 3, n = 7
输出：28
```

**示例 2：**

```
输入：m = 3, n = 2
输出：3
解释：
从左上角开始，总共有 3 条路径可以到达右下角。
1. 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右
3. 向下 -> 向右 -> 向下
```

**示例 3：**

```
输入：m = 7, n = 3
输出：28
```

**示例 4：**

```
输入：m = 3, n = 3
输出：6
```

 

**提示：**

- `1 <= m, n <= 100`
- 题目数据保证答案小于等于 `2 * 109`

**思路：（动态规划）**
构建二维数组`dp[m][n]`，元素`dp[i][j]`表示到位置`(i,j)`的方格的路径数。根据题目要求，第一行和第一列只有一种路径可以到达，其余位置的到达路径可以从左侧到达，也可以从上方到达，因此为`dp[i][j] = dp[i-1][j] + dp[i][j-1]`。

**实现：**
```
int uniquePaths(int m, int n){
    if (m == 0 || n == 0) {
        return 0;
    }
    int dp[100][100];
    dp[0][0] = 1;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (i == 0 || j == 0) {
                dp[i][j] = 1;
            } else {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
    }
    return dp[m - 1][n - 1];
}
```

---------------------
## 困难

#### [84. 柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

**难度：**困难

给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/histogram.png)

以上是柱状图的示例，其中每个柱子的宽度为 1，给定的高度为 `[2,1,5,6,2,3]`。 

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/histogram_area.png)

图中阴影部分为所能勾勒出的最大矩形面积，其面积为 `10` 个单位。 

**示例:**

```
输入: [2,1,5,6,2,3]
输出: 10
```

**思路：**

使用单调栈，分别找出每个位置的左、右边界。

1. 先把第一个元素`2`的下标0入栈
2. 第2个元素`1`比当前栈顶元素`2`的高度小，故以栈顶元素作为高度，其右边界至当前遍历的元素（下标为1）截止，使用数组记录栈顶元素`2`的下标(0)对应的右边界，然后`2`的下标出栈，`1`的下标进栈
3. 第3个元素`5`比当前栈顶元素的高度`1`大，直接入栈，表示以当前元素作为高度是可以作为拓展到当前位置的
4. 第4个元素`6`入栈，同上
5. 第5个元素`2`比栈顶元素的下标`6`小，`6`出栈，同时记录栈顶下标对应的右边界
6. 如此反复，当遍历完，栈中剩余元素的下标都是数组的右边界

```c++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int len = heights.size();
        stack<int> stk;
        vector<int> right_border(len);
        stk.push(0);
        for (int i = 1; i < len; ++i) {
            while (!stk.empty() && heights[i] < heights[stk.top()]) {
                right_border[stk.top()] = i;
                stk.pop();
            }
            stk.push(i);
        }
        while (!stk.empty()) {
            right_border[stk.top()] = len;
            stk.pop();
        }
        vector<int> left_border(len);
        stk.push(len - 1);
        for (int i = len - 2; i >= 0; --i) {
            while (!stk.empty() && heights[i] < heights[stk.top()]) {
                left_border[stk.top()] = i;
                stk.pop();
            }
            stk.push(i);
        }
        while (!stk.empty()) {
            left_border[stk.top()] = -1;
            stk.pop();
        }
        int max_square = 0, square = 0;
        for (int i = 0; i < len; ++i) {
            // cout << i << ":" << left_border[i] << "," << right_border[i] << endl;
            square = (right_border[i] - left_border[i] - 1) * heights[i];
            max_square = max(max_square, square);
        }
        return max_square;
    }
};
```

----------------------------

#### [84. 最大矩形](https://leetcode-cn.com/problems/maximal-rectangle/)

**难度：**困难

给定一个仅包含 `0` 和 `1` 、大小为 `rows x cols` 的二维二进制矩阵，找出只包含 `1` 的最大矩形，并返回其面积。

 

**示例 1：**

![](https://assets.leetcode.com/uploads/2020/09/14/maximal.jpg)

```
输入：matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
输出：6
解释：最大矩形如上图所示。
```

**示例 2：**

```
输入：matrix = []
输出：0
```

**示例 3：**

```
输入：matrix = [["0"]]
输出：0
```

**示例 4：**

```
输入：matrix = [["1"]]
输出：1
```

**示例 5：**

```
输入：matrix = [["0","0"]]
输出：0
```
