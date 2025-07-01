# 动态规划 (Dynamic Programming)

## 概述

动态规划是算法设计中最重要的技巧之一，通过将复杂问题分解为子问题，并存储子问题的解来避免重复计算，从而大幅提升算法效率。DP是程序员面试中的高频考点，掌握DP的思想和常见模型对面试至关重要。

## 🎯 核心概念

### 动态规划的特征

1. **重叠子问题 (Overlapping Subproblems)**
   - 问题可以分解为子问题
   - 子问题会被反复求解
   - 通过存储结果避免重复计算

2. **最优子结构 (Optimal Substructure)**
   - 问题的最优解包含子问题的最优解
   - 可以从子问题的最优解构造出原问题的最优解

3. **无后效性 (No Aftereffect)**
   - 子问题的解一旦确定，就不再改变
   - 当前状态只依赖于之前的状态

### 动态规划 vs 分治算法

| 特性 | 动态规划 | 分治算法 |
|------|---------|---------|
| 子问题重叠 | 是 | 否 |
| 存储中间结果 | 是 | 否 |
| 时间复杂度 | 通常较低 | 可能有重复计算 |
| 适用场景 | 优化问题 | 大问题分解 |

## 📝 解题步骤

### 动态规划五步法

1. **确定状态定义**
   - 明确dp数组的含义
   - 确定状态变量的维度

2. **找出状态转移方程**
   - 分析当前状态与之前状态的关系
   - 写出递推公式

3. **初始化边界条件**
   - 确定dp数组的初始值
   - 处理边界情况

4. **确定遍历顺序**
   - 保证计算当前状态时，所依赖的状态已经计算出来

5. **返回结果**
   - 根据问题要求返回相应的dp值

## 🔥 经典问题类型

### 1. 线性DP

#### 1.1 爬楼梯问题

**问题描述**: 爬到第n阶楼梯，每次可以爬1或2个台阶，有多少种方法？

```java
public class ClimbingStairs {
    // 方法1：基础递归（会超时）
    public int climbStairsRecursive(int n) {
        if (n <= 2) return n;
        return climbStairsRecursive(n - 1) + climbStairsRecursive(n - 2);
    }
    
    // 方法2：记忆化搜索
    public int climbStairsMemo(int n) {
        int[] memo = new int[n + 1];
        return helper(n, memo);
    }
    
    private int helper(int n, int[] memo) {
        if (n <= 2) return n;
        if (memo[n] != 0) return memo[n];
        
        memo[n] = helper(n - 1, memo) + helper(n - 2, memo);
        return memo[n];
    }
    
    // 方法3：动态规划
    public int climbStairsDP(int n) {
        if (n <= 2) return n;
        
        int[] dp = new int[n + 1];
        dp[1] = 1;
        dp[2] = 2;
        
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        
        return dp[n];
    }
    
    // 方法4：空间优化
    public int climbStairsOptimized(int n) {
        if (n <= 2) return n;
        
        int prev2 = 1, prev1 = 2;
        
        for (int i = 3; i <= n; i++) {
            int current = prev1 + prev2;
            prev2 = prev1;
            prev1 = current;
        }
        
        return prev1;
    }
}
```

#### 1.2 最大子数组和 (Kadane's Algorithm)

```java
public class MaximumSubarray {
    // 动态规划解法
    public int maxSubArray(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n]; // dp[i]表示以nums[i]结尾的最大子数组和
        dp[0] = nums[0];
        int maxSum = dp[0];
        
        for (int i = 1; i < n; i++) {
            dp[i] = Math.max(nums[i], dp[i - 1] + nums[i]);
            maxSum = Math.max(maxSum, dp[i]);
        }
        
        return maxSum;
    }
    
    // 空间优化版本
    public int maxSubArrayOptimized(int[] nums) {
        int maxSum = nums[0];
        int currentSum = nums[0];
        
        for (int i = 1; i < nums.length; i++) {
            currentSum = Math.max(nums[i], currentSum + nums[i]);
            maxSum = Math.max(maxSum, currentSum);
        }
        
        return maxSum;
    }
    
    // 返回最大子数组的起始和结束位置
    public int[] maxSubArrayWithIndices(int[] nums) {
        int maxSum = nums[0];
        int currentSum = nums[0];
        int start = 0, end = 0, tempStart = 0;
        
        for (int i = 1; i < nums.length; i++) {
            if (currentSum < 0) {
                currentSum = nums[i];
                tempStart = i;
            } else {
                currentSum += nums[i];
            }
            
            if (currentSum > maxSum) {
                maxSum = currentSum;
                start = tempStart;
                end = i;
            }
        }
        
        return new int[]{start, end, maxSum};
    }
}
```

#### 1.3 最长递增子序列 (LIS)

```java
public class LongestIncreasingSubsequence {
    // 动态规划：O(n²)
    public int lengthOfLIS(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n]; // dp[i]表示以nums[i]结尾的LIS长度
        Arrays.fill(dp, 1);
        
        int maxLength = 1;
        
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            maxLength = Math.max(maxLength, dp[i]);
        }
        
        return maxLength;
    }
    
    // 二分搜索优化：O(n log n)
    public int lengthOfLISBinarySearch(int[] nums) {
        List<Integer> tails = new ArrayList<>();
        
        for (int num : nums) {
            int pos = binarySearch(tails, num);
            if (pos == tails.size()) {
                tails.add(num);
            } else {
                tails.set(pos, num);
            }
        }
        
        return tails.size();
    }
    
    private int binarySearch(List<Integer> tails, int target) {
        int left = 0, right = tails.size();
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (tails.get(mid) < target) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        
        return left;
    }
    
    // 打印LIS序列
    public List<Integer> findLIS(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        int[] parent = new int[n];
        Arrays.fill(dp, 1);
        Arrays.fill(parent, -1);
        
        int maxLength = 1;
        int maxIndex = 0;
        
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i] && dp[j] + 1 > dp[i]) {
                    dp[i] = dp[j] + 1;
                    parent[i] = j;
                }
            }
            
            if (dp[i] > maxLength) {
                maxLength = dp[i];
                maxIndex = i;
            }
        }
        
        // 重建LIS
        List<Integer> lis = new ArrayList<>();
        int current = maxIndex;
        while (current != -1) {
            lis.add(nums[current]);
            current = parent[current];
        }
        
        Collections.reverse(lis);
        return lis;
    }
}
```

### 2. 背包DP

#### 2.1 0-1背包问题

```java
public class Knapsack01 {
    // 二维DP
    public int knapsack(int[] weights, int[] values, int capacity) {
        int n = weights.length;
        int[][] dp = new int[n + 1][capacity + 1];
        
        for (int i = 1; i <= n; i++) {
            for (int w = 1; w <= capacity; w++) {
                if (weights[i - 1] <= w) {
                    // 可以选择第i个物品
                    dp[i][w] = Math.max(
                        dp[i - 1][w], // 不选择
                        dp[i - 1][w - weights[i - 1]] + values[i - 1] // 选择
                    );
                } else {
                    // 不能选择第i个物品
                    dp[i][w] = dp[i - 1][w];
                }
            }
        }
        
        return dp[n][capacity];
    }
    
    // 一维DP（空间优化）
    public int knapsackOptimized(int[] weights, int[] values, int capacity) {
        int[] dp = new int[capacity + 1];
        
        for (int i = 0; i < weights.length; i++) {
            // 逆序遍历，避免重复使用同一物品
            for (int w = capacity; w >= weights[i]; w--) {
                dp[w] = Math.max(dp[w], dp[w - weights[i]] + values[i]);
            }
        }
        
        return dp[capacity];
    }
    
    // 返回选择的物品
    public List<Integer> knapsackItems(int[] weights, int[] values, int capacity) {
        int n = weights.length;
        int[][] dp = new int[n + 1][capacity + 1];
        
        // 填充DP表
        for (int i = 1; i <= n; i++) {
            for (int w = 1; w <= capacity; w++) {
                if (weights[i - 1] <= w) {
                    dp[i][w] = Math.max(
                        dp[i - 1][w],
                        dp[i - 1][w - weights[i - 1]] + values[i - 1]
                    );
                } else {
                    dp[i][w] = dp[i - 1][w];
                }
            }
        }
        
        // 回溯找出选择的物品
        List<Integer> items = new ArrayList<>();
        int w = capacity;
        for (int i = n; i > 0 && w > 0; i--) {
            if (dp[i][w] != dp[i - 1][w]) {
                items.add(i - 1); // 物品索引
                w -= weights[i - 1];
            }
        }
        
        Collections.reverse(items);
        return items;
    }
}
```

#### 2.2 完全背包问题

```java
public class UnboundedKnapsack {
    // 每种物品可以选择无限次
    public int unboundedKnapsack(int[] weights, int[] values, int capacity) {
        int[] dp = new int[capacity + 1];
        
        for (int i = 0; i < weights.length; i++) {
            // 正序遍历，允许重复使用同一物品
            for (int w = weights[i]; w <= capacity; w++) {
                dp[w] = Math.max(dp[w], dp[w - weights[i]] + values[i]);
            }
        }
        
        return dp[capacity];
    }
    
    // 找零钱问题（完全背包的变形）
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, amount + 1); // 初始化为不可能的值
        dp[0] = 0;
        
        for (int coin : coins) {
            for (int i = coin; i <= amount; i++) {
                dp[i] = Math.min(dp[i], dp[i - coin] + 1);
            }
        }
        
        return dp[amount] > amount ? -1 : dp[amount];
    }
    
    // 找零钱的方案数
    public int coinChangeWays(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;
        
        for (int coin : coins) {
            for (int i = coin; i <= amount; i++) {
                dp[i] += dp[i - coin];
            }
        }
        
        return dp[amount];
    }
}
```

### 3. 区间DP

#### 3.1 最长公共子序列 (LCS)

```java
public class LongestCommonSubsequence {
    // 二维DP
    public int longestCommonSubsequence(String text1, String text2) {
        int m = text1.length(), n = text2.length();
        int[][] dp = new int[m + 1][n + 1];
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        
        return dp[m][n];
    }
    
    // 空间优化
    public int longestCommonSubsequenceOptimized(String text1, String text2) {
        int m = text1.length(), n = text2.length();
        int[] dp = new int[n + 1];
        
        for (int i = 1; i <= m; i++) {
            int prev = 0;
            for (int j = 1; j <= n; j++) {
                int temp = dp[j];
                if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                    dp[j] = prev + 1;
                } else {
                    dp[j] = Math.max(dp[j], dp[j - 1]);
                }
                prev = temp;
            }
        }
        
        return dp[n];
    }
    
    // 构造LCS字符串
    public String constructLCS(String text1, String text2) {
        int m = text1.length(), n = text2.length();
        int[][] dp = new int[m + 1][n + 1];
        
        // 填充DP表
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        
        // 回溯构造LCS
        StringBuilder lcs = new StringBuilder();
        int i = m, j = n;
        
        while (i > 0 && j > 0) {
            if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                lcs.append(text1.charAt(i - 1));
                i--;
                j--;
            } else if (dp[i - 1][j] > dp[i][j - 1]) {
                i--;
            } else {
                j--;
            }
        }
        
        return lcs.reverse().toString();
    }
}
```

#### 3.2 编辑距离

```java
public class EditDistance {
    public int minDistance(String word1, String word2) {
        int m = word1.length(), n = word2.length();
        int[][] dp = new int[m + 1][n + 1];
        
        // 初始化边界
        for (int i = 0; i <= m; i++) {
            dp[i][0] = i; // 删除i个字符
        }
        for (int j = 0; j <= n; j++) {
            dp[0][j] = j; // 插入j个字符
        }
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1]; // 不需要操作
                } else {
                    dp[i][j] = Math.min(
                        Math.min(
                            dp[i - 1][j] + 1,      // 删除
                            dp[i][j - 1] + 1       // 插入
                        ),
                        dp[i - 1][j - 1] + 1       // 替换
                    );
                }
            }
        }
        
        return dp[m][n];
    }
    
    // 空间优化版本
    public int minDistanceOptimized(String word1, String word2) {
        int m = word1.length(), n = word2.length();
        int[] dp = new int[n + 1];
        
        // 初始化
        for (int j = 0; j <= n; j++) {
            dp[j] = j;
        }
        
        for (int i = 1; i <= m; i++) {
            int prev = dp[0];
            dp[0] = i;
            
            for (int j = 1; j <= n; j++) {
                int temp = dp[j];
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[j] = prev;
                } else {
                    dp[j] = Math.min(Math.min(dp[j], dp[j - 1]), prev) + 1;
                }
                prev = temp;
            }
        }
        
        return dp[n];
    }
}
```

### 4. 状态机DP

#### 4.1 买卖股票问题

```java
public class StockProblems {
    // 最多一次交易
    public int maxProfit(int[] prices) {
        int minPrice = prices[0];
        int maxProfit = 0;
        
        for (int i = 1; i < prices.length; i++) {
            maxProfit = Math.max(maxProfit, prices[i] - minPrice);
            minPrice = Math.min(minPrice, prices[i]);
        }
        
        return maxProfit;
    }
    
    // 无限次交易
    public int maxProfitUnlimited(int[] prices) {
        int profit = 0;
        
        for (int i = 1; i < prices.length; i++) {
            if (prices[i] > prices[i - 1]) {
                profit += prices[i] - prices[i - 1];
            }
        }
        
        return profit;
    }
    
    // 最多k次交易
    public int maxProfitK(int k, int[] prices) {
        int n = prices.length;
        if (k >= n / 2) {
            return maxProfitUnlimited(prices);
        }
        
        // dp[i][j][0]表示第i天，完成j次交易，不持有股票的最大利润
        // dp[i][j][1]表示第i天，完成j次交易，持有股票的最大利润
        int[][][] dp = new int[n][k + 1][2];
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= k; j++) {
                if (i == 0) {
                    dp[i][j][0] = 0;
                    dp[i][j][1] = -prices[0];
                    continue;
                }
                
                dp[i][j][0] = Math.max(dp[i - 1][j][0], dp[i - 1][j][1] + prices[i]);
                
                if (j > 0) {
                    dp[i][j][1] = Math.max(dp[i - 1][j][1], dp[i - 1][j - 1][0] - prices[i]);
                } else {
                    dp[i][j][1] = Math.max(dp[i - 1][j][1], -prices[i]);
                }
            }
        }
        
        return dp[n - 1][k][0];
    }
    
    // 含冷冻期
    public int maxProfitWithCooldown(int[] prices) {
        int n = prices.length;
        if (n <= 1) return 0;
        
        // hold: 持有股票, sold: 刚卖出股票, rest: 不持有股票且不在冷冻期
        int hold = -prices[0];
        int sold = 0;
        int rest = 0;
        
        for (int i = 1; i < n; i++) {
            int prevHold = hold;
            int prevSold = sold;
            int prevRest = rest;
            
            hold = Math.max(prevHold, prevRest - prices[i]);
            sold = prevHold + prices[i];
            rest = Math.max(prevRest, prevSold);
        }
        
        return Math.max(sold, rest);
    }
}
```

## 🎯 DP优化技巧

### 1. 空间优化

```java
// 从二维优化到一维
// 原始二维DP
int[][] dp = new int[m][n];

// 优化为一维（当前状态只依赖上一行）
int[] dp = new int[n];
int[] prev = new int[n];

// 或者使用滚动数组
int[][] dp = new int[2][n];
```

### 2. 状态压缩

```java
// 使用位运算压缩状态
// 例如：旅行商问题中用整数表示访问过的城市集合
int state = 0;
state |= (1 << city); // 访问城市city
boolean visited = (state & (1 << city)) != 0; // 检查是否访问过city
```

### 3. 记忆化搜索

```java
public class MemorizedSearch {
    private Map<String, Integer> memo = new HashMap<>();
    
    public int solve(int[] arr, int index, int target) {
        String key = index + "," + target;
        if (memo.containsKey(key)) {
            return memo.get(key);
        }
        
        // 递归计算
        int result = calculateResult(arr, index, target);
        memo.put(key, result);
        return result;
    }
    
    private int calculateResult(int[] arr, int index, int target) {
        // 具体的递归逻辑
        return 0;
    }
}
```

## 🚀 面试技巧

### 1. 识别DP问题的信号
- 求最值（最大、最小、最长、最短）
- 求方案数
- 求可行性（是否存在一种方案）
- 具有重叠子问题
- 具有最优子结构

### 2. 常见DP模型
- **线性DP**: 在一维数组上的DP
- **区间DP**: 在区间上的DP，通常涉及回文、最优分割
- **背包DP**: 选择物品的优化问题
- **树形DP**: 在树上的DP，通常涉及子树问题
- **状态机DP**: 有限状态的转换问题
- **数位DP**: 与数字的每一位相关的问题

### 3. 解题步骤
1. **暴力递归**: 先写出暴力解法，理解问题结构
2. **记忆化**: 添加缓存避免重复计算
3. **动态规划**: 改为自底向上的迭代实现
4. **空间优化**: 分析状态依赖关系，优化空间复杂度

## 📚 练习题目

### 基础题目
- [爬楼梯](https://leetcode.com/problems/climbing-stairs/)
- [最大子数组和](https://leetcode.com/problems/maximum-subarray/)
- [买卖股票的最佳时机](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

### 进阶题目  
- [最长递增子序列](https://leetcode.com/problems/longest-increasing-subsequence/)
- [零钱兑换](https://leetcode.com/problems/coin-change/)
- [最长公共子序列](https://leetcode.com/problems/longest-common-subsequence/)

### 困难题目
- [编辑距离](https://leetcode.com/problems/edit-distance/)
- [正则表达式匹配](https://leetcode.com/problems/regular-expression-matching/)
- [最长有效括号](https://leetcode.com/problems/longest-valid-parentheses/)

## 📖 参考资料

1. **《算法导论》第15章** - 动态规划
2. **《编程珠玑》** - 动态规划思想
3. [LeetCode DP Problems](https://leetcode.com/tag/dynamic-programming/) - 在线练习
4. [TopCoder DP Tutorial](https://www.topcoder.com/community/competitive-programming/tutorials/dynamic-programming-from-novice-to-advanced/) - 详细教程

---

**上一章**: [算法设计总览](./overview.md)  
**下一章**: [贪心算法](./greedy.md) 