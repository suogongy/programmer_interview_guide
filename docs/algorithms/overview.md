# 算法设计 - 总览

## 概述

算法设计是计算机科学的核心，是解决问题的系统化方法。良好的算法设计能够大幅提升程序性能，是程序员面试中最重要的考查内容。本章节涵盖了面试中最常见的算法设计技巧和经典算法。

## 📚 章节内容

### 1. [排序算法 (Sorting Algorithms)](./sorting.md)
- 比较排序：快速排序、归并排序、堆排序
- 非比较排序：计数排序、基数排序、桶排序
- 排序算法的选择与优化
- 稳定性分析与应用场景

### 2. [搜索算法 (Search Algorithms)](./searching.md)
- 线性搜索与二分搜索
- 二分搜索的变形与应用
- 搜索空间的定义与缩减
- 在旋转数组中搜索

### 3. [动态规划 (Dynamic Programming)](./dynamic-programming.md)
- DP的核心思想与步骤
- 经典DP问题（背包、LCS、LIS等）
- 状态压缩与空间优化
- 记忆化搜索

### 4. [贪心算法 (Greedy Algorithms)](./greedy.md)
- 贪心策略的设计与证明
- 活动选择、区间调度
- 最小生成树算法
- 哈夫曼编码

### 5. [分治算法 (Divide and Conquer)](./divide-conquer.md)
- 分治思想与递归关系
- 归并排序、快速排序的分治实现
- 最大子数组、大整数乘法
- 主定理与复杂度分析

### 6. [回溯算法 (Backtracking)](./backtracking.md)
- 回溯的核心框架
- 排列、组合、子集生成
- N皇后、数独求解
- 剪枝策略与优化

### 7. [图算法 (Graph Algorithms)](./graph-algorithms.md)
- 图的表示与遍历（DFS、BFS）
- 最短路径算法（Dijkstra、Floyd）
- 最小生成树（Kruskal、Prim）
- 拓扑排序与强连通分量

### 8. [字符串算法 (String Algorithms)](./string-algorithms.md)
- 字符串匹配（KMP、Rabin-Karp）
- 最长公共前缀/后缀
- 回文串检测与计数
- 字符串编辑距离

## 🎯 算法复杂度对比

### 时间复杂度层级
```
O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(n³) < O(2ⁿ) < O(n!)
```

### 常见算法复杂度
| 算法类型 | 最好情况 | 平均情况 | 最坏情况 | 空间复杂度 |
|---------|---------|---------|---------|-----------|
| 快速排序 | O(n log n) | O(n log n) | O(n²) | O(log n) |
| 归并排序 | O(n log n) | O(n log n) | O(n log n) | O(n) |
| 堆排序 | O(n log n) | O(n log n) | O(n log n) | O(1) |
| 二分搜索 | O(1) | O(log n) | O(log n) | O(1) |
| 背包DP | - | O(nW) | O(nW) | O(nW) |
| 图DFS/BFS | - | O(V+E) | O(V+E) | O(V) |

## 🧠 算法设计技巧

### 1. 问题分析步骤
1. **理解问题**: 明确输入输出，识别约束条件
2. **寻找模式**: 判断问题类型，选择合适的算法范式
3. **设计算法**: 构建解决方案，分析时间空间复杂度
4. **优化改进**: 考虑边界情况，优化算法效率

### 2. 常见优化策略
- **时间优化**: 预计算、记忆化、数据结构选择
- **空间优化**: 原地算法、状态压缩、循环覆盖
- **常数优化**: 位运算、缓存友好、分支预测

### 3. 复杂度分析方法
- **主定理**: 分治算法的复杂度分析
- **摊销分析**: 动态数组、并查集
- **概率分析**: 随机算法的期望复杂度

## 💡 算法学习路径

### 基础阶段 (2-4周)
1. **排序搜索**: 掌握基本排序和二分搜索
2. **基础DP**: 斐波那契、爬楼梯、最大子数组
3. **简单贪心**: 活动选择、找零钱
4. **基础回溯**: 全排列、组合

### 进阶阶段 (4-6周)
1. **高级DP**: 背包问题、LCS、编辑距离
2. **图算法**: DFS/BFS、最短路径
3. **字符串**: KMP、回文串
4. **数学算法**: 质数、GCD、快速幂

### 高级阶段 (2-4周)
1. **高级数据结构**: 线段树、树状数组
2. **网络流**: 最大流、最小割
3. **计算几何**: 凸包、线段相交
4. **高级DP**: 数位DP、概率DP

## 📝 面试中的算法问题

### 高频算法类型
1. **数组/字符串** (40%): 双指针、滑动窗口、前缀和
2. **树/图** (25%): 遍历、路径问题、最短路径
3. **动态规划** (20%): 经典DP模型及其变形
4. **排序/搜索** (10%): 二分搜索、排序应用
5. **其他** (5%): 数学、位运算、设计类

### 解题框架模板

```python
def solve_problem(input_data):
    # 1. 边界情况检查
    if not input_data:
        return default_result
    
    # 2. 初始化变量
    # 根据算法类型初始化所需变量
    
    # 3. 核心算法逻辑
    # 实现具体的算法
    
    # 4. 返回结果
    return result
```

### 动态规划模板
```python
def dp_solution(problem_input):
    # 1. 定义状态
    # dp[i] 表示...
    
    # 2. 初始化
    dp = [initial_value] * size
    
    # 3. 状态转移
    for i in range(start, end):
        dp[i] = function(dp[previous_states])
    
    # 4. 返回结果
    return dp[target_state]
```

### 回溯算法模板
```python
def backtrack_solution(candidates, target):
    result = []
    path = []
    
    def backtrack(start_index):
        # 终止条件
        if meets_condition(path):
            result.append(path[:])
            return
        
        # 遍历选择
        for i in range(start_index, len(candidates)):
            # 做选择
            path.append(candidates[i])
            
            # 递归
            backtrack(i + 1)  # 或 backtrack(i) 允许重复
            
            # 撤销选择
            path.pop()
    
    backtrack(0)
    return result
```

## 🎯 面试准备策略

### 刷题规划
1. **基础题目** (100题): LeetCode Easy/Medium
2. **经典题目** (50题): 高频面试题
3. **困难题目** (30题): Hard题目，提升思维
4. **专题练习**: 按算法类型分类练习

### 代码实现要点
1. **边界处理**: 空输入、单元素、重复元素
2. **变量命名**: 有意义的变量名，提高可读性
3. **注释说明**: 关键步骤加注释
4. **测试用例**: 考虑各种corner case

### 面试技巧
1. **思路表达**: 先说思路再写代码
2. **复杂度分析**: 主动分析时间空间复杂度
3. **代码优化**: 展示优化思路
4. **问题澄清**: 主动确认题目细节

## 🔗 学习资源

### 经典教材
1. **《算法导论》** - Cormen等，算法理论权威
2. **《算法》第4版** - Sedgewick，Java实现
3. **《编程珠玑》** - Bentley，算法设计技巧
4. **《算法设计》** - Kleinberg，算法设计方法

### 在线资源
1. [LeetCode](https://leetcode.com/) - 在线练习平台
2. [Algorithm Visualizer](https://algorithm-visualizer.org/) - 算法可视化
3. [Visualgo](https://visualgo.net/) - 数据结构和算法可视化
4. [GeeksforGeeks](https://www.geeksforgeeks.org/) - 算法学习资源

### 视频课程
1. [MIT 6.006](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-006-introduction-to-algorithms-fall-2011/) - 算法导论
2. [Princeton Algorithms](https://www.coursera.org/learn/algorithms-part1) - Coursera
3. [Stanford CS161](https://web.stanford.edu/class/cs161/) - 算法设计与分析

## 🚀 实战演练

### 必刷题目清单

#### 排序与搜索
- [排序数组](https://leetcode.com/problems/sort-an-array/)
- [搜索二维矩阵](https://leetcode.com/problems/search-a-2d-matrix/)
- [寻找旋转排序数组中的最小值](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

#### 动态规划
- [爬楼梯](https://leetcode.com/problems/climbing-stairs/)
- [最大子数组和](https://leetcode.com/problems/maximum-subarray/)
- [最长递增子序列](https://leetcode.com/problems/longest-increasing-subsequence/)

#### 回溯算法
- [全排列](https://leetcode.com/problems/permutations/)
- [组合总和](https://leetcode.com/problems/combination-sum/)
- [N皇后](https://leetcode.com/problems/n-queens/)

#### 图算法
- [岛屿数量](https://leetcode.com/problems/number-of-islands/)
- [课程表](https://leetcode.com/problems/course-schedule/)
- [网络延迟时间](https://leetcode.com/problems/network-delay-time/)

---

## 🔗 快速导航

通过系统学习这些算法设计技巧，您将具备解决复杂编程问题的能力。建议按照章节顺序学习，并结合大量练习来巩固理解。

**下一章**: [排序算法 (Sorting Algorithms)](./sorting.md) 