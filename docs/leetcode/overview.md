# LeetCode刷题指南 - 总览

## 概述

LeetCode是程序员面试准备的必备平台，涵盖了算法、数据结构、系统设计等多个方面。本章节提供系统化的刷题策略、高频题型分析和解题技巧，帮助您高效准备技术面试。

## 📚 章节内容

### 1. [题型分类与解题思路](./problem-types.md)
- **数组与字符串**: 双指针、滑动窗口、前缀和
- **链表**: 快慢指针、反转、合并
- **栈与队列**: 单调栈、优先队列
- **树与图**: DFS/BFS、最短路径
- **动态规划**: 状态定义、转移方程
- **回溯算法**: 排列组合、N皇后

### 2. [每日一题推荐](./daily-practice.md)
- **入门阶段** (1-2个月): Easy题目为主
- **进阶阶段** (2-3个月): Medium题目训练
- **冲刺阶段** (1个月): Hard题目挑战
- **维护阶段**: 高频题复习巩固

### 3. [高频面试题解析](./top-questions.md)
- **Top 100**: 最常考的100道题详解
- **按公司分类**: FAANG、字节、阿里等
- **按难度分类**: Easy/Medium/Hard分层
- **按标签分类**: Array、Tree、DP等

### 4. [时间复杂度优化技巧](./optimization.md)
- **空间换时间**: 哈希表、预计算
- **数学优化**: 位运算、数学公式
- **算法选择**: 贪心vs动态规划
- **数据结构选择**: 合适的数据结构

## 🎯 刷题策略

### 阶段化学习路径

#### 第一阶段：基础入门 (200题)
- **目标**: 掌握基本算法和数据结构
- **时间**: 2-3个月
- **重点**: Array、String、LinkedList、Stack、Queue

#### 第二阶段：进阶提升 (300题)  
- **目标**: 熟练运用各种算法技巧
- **时间**: 3-4个月
- **重点**: Tree、Graph、DP、Backtracking

#### 第三阶段：高级冲刺 (200题)
- **目标**: 解决复杂问题，提升速度
- **时间**: 2-3个月
- **重点**: Hard题目、系统设计、面试技巧

### 按公司准备策略

#### FAANG公司
- **Google**: 算法题为主，重视代码质量
- **Facebook**: 行为面试+算法，重视沟通
- **Amazon**: LP原则+算法，重视领导力
- **Apple**: 系统设计+算法，重视细节
- **Netflix**: 高级算法，重视创新思维

#### 国内大厂
- **字节跳动**: 算法难度较高，重视思维过程
- **阿里巴巴**: 业务理解+算法，重视工程能力
- **腾讯**: 游戏/社交场景算法，重视用户体验
- **百度**: AI相关算法，重视技术深度

## 🔥 高频题型详解

### 1. 数组类问题 (40%面试出现率)

#### 双指针技巧
```python
# 两数之和 - 排序数组版本
def twoSum(nums, target):
    left, right = 0, len(nums) - 1
    while left < right:
        current_sum = nums[left] + nums[right]
        if current_sum == target:
            return [left, right]
        elif current_sum < target:
            left += 1
        else:
            right -= 1
    return []
```

#### 滑动窗口
```python
# 最长无重复字符子串
def lengthOfLongestSubstring(s):
    char_set = set()
    left = 0
    max_len = 0
    
    for right in range(len(s)):
        while s[right] in char_set:
            char_set.remove(s[left])
            left += 1
        char_set.add(s[right])
        max_len = max(max_len, right - left + 1)
    
    return max_len
```

### 2. 树类问题 (25%面试出现率)

#### 树的遍历
```python
# 二叉树中序遍历 - 递归版本
def inorderTraversal(root):
    result = []
    def inorder(node):
        if node:
            inorder(node.left)
            result.append(node.val)
            inorder(node.right)
    inorder(root)
    return result

# 二叉树中序遍历 - 迭代版本
def inorderTraversal(root):
    result = []
    stack = []
    current = root
    
    while stack or current:
        while current:
            stack.append(current)
            current = current.left
        current = stack.pop()
        result.append(current.val)
        current = current.right
    
    return result
```

### 3. 动态规划问题 (20%面试出现率)

#### 经典DP模板
```python
# 最长公共子序列
def longestCommonSubsequence(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    return dp[m][n]
```

## 📈 刷题进度规划

### 每日刷题计划

#### 入门阶段 (每天2-3题)
- **Week 1-2**: Array、String基础题
- **Week 3-4**: LinkedList、Stack、Queue
- **Week 5-6**: 简单的Tree、Hash Table
- **Week 7-8**: 基础数学、位运算

#### 进阶阶段 (每天2题)
- **Week 9-12**: Tree进阶、Graph基础
- **Week 13-16**: DP入门、Backtracking
- **Week 17-20**: 二分搜索、双指针进阶
- **Week 21-24**: 贪心算法、复杂DP

#### 冲刺阶段 (每天1-2题)
- **Week 25-28**: Hard题目训练
- **Week 29-32**: 模拟面试、查漏补缺
- **Week 33-36**: 公司真题、系统复习

### 复习策略

#### 艾宾浩斯遗忘曲线复习法
- **第1天**: 做新题
- **第2天**: 复习昨天的题
- **第4天**: 复习3天前的题  
- **第7天**: 复习1周前的题
- **第15天**: 复习2周前的题
- **第30天**: 复习1月前的题

## 💡 解题技巧与模板

### 解题四步法

#### 1. 理解题意
- 明确输入输出格式
- 理解约束条件
- 考虑边界情况
- 举例验证理解

#### 2. 分析问题
- 识别问题类型
- 选择合适算法
- 估算时间复杂度
- 考虑空间优化

#### 3. 编写代码
- 先写框架再填细节
- 处理边界情况
- 变量命名清晰
- 适当添加注释

#### 4. 测试验证
- 用样例测试
- 考虑边界情况
- 检查逻辑错误
- 优化代码质量

### 常用代码模板

#### DFS模板
```python
def dfs(node, visited, result):
    if node in visited:
        return
    
    visited.add(node)
    # 处理当前节点
    result.append(node.val)
    
    # 遍历邻居节点
    for neighbor in node.neighbors:
        dfs(neighbor, visited, result)
```

#### BFS模板
```python
from collections import deque

def bfs(start):
    queue = deque([start])
    visited = set([start])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node.val)
        
        for neighbor in node.neighbors:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return result
```

## 📊 面试统计数据

### 按题型统计 (基于真实面试数据)

| 题型 | 出现频率 | 难度分布 | 推荐练习量 |
|------|---------|---------|-----------|
| **Array/String** | 40% | E:M:H = 3:5:2 | 80题 |
| **Tree/Graph** | 25% | E:M:H = 2:6:2 | 60题 |
| **Dynamic Programming** | 20% | E:M:H = 1:6:3 | 50题 |
| **LinkedList** | 15% | E:M:H = 4:5:1 | 30题 |
| **Math/Bit** | 10% | E:M:H = 3:5:2 | 25题 |
| **Design** | 5% | E:M:H = 1:4:5 | 15题 |

### 公司偏好分析

#### Google
- **偏好**: 算法思维、代码质量
- **高频标签**: Array, Tree, DP, Math
- **难度**: Medium为主，30%Hard

#### Meta (Facebook)  
- **偏好**: 产品思维、系统设计
- **高频标签**: Tree, Graph, String
- **难度**: Medium为主，注重沟通

## 🎯 面试准备checklist

### 技术准备
- [ ] 完成目标刷题数量
- [ ] 熟练掌握常用算法
- [ ] 能够分析时间空间复杂度
- [ ] 掌握代码优化技巧

### 软技能准备  
- [ ] 练习口头表达算法思路
- [ ] 能够清晰解释代码逻辑
- [ ] 学会与面试官互动
- [ ] 准备面试常见问题

### 心理准备
- [ ] 保持良好作息
- [ ] 控制面试紧张情绪  
- [ ] 准备应对挫折
- [ ] 保持学习热情

## 📖 推荐资源

### 在线平台
1. [LeetCode](https://leetcode.com/) - 主要刷题平台
2. [LeetCode中国](https://leetcode.cn/) - 中文版本
3. [牛客网](https://www.nowcoder.com/) - 国内面试平台
4. [Codeforces](https://codeforces.com/) - 算法竞赛

### 学习资料
1. **《剑指Offer》** - 经典面试题集
2. **《程序员代码面试指南》** - 左程云著
3. **《算法导论》** - 理论基础
4. **《LeetCode 101》** - 入门指南

### 视频课程
1. [花花酱LeetCode](https://www.youtube.com/user/xxfflower) - 中文讲解
2. [Back To Back SWE](https://www.youtube.com/channel/UCmJz2DV1a3yfgrR7GqRtUUA) - 英文讲解
3. [NeetCode](https://neetcode.io/) - 系统化课程

---

通过系统化的LeetCode练习，您将建立扎实的算法基础，提升解决复杂问题的能力，为技术面试做好充分准备。 