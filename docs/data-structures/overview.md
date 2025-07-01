# 核心数据结构 - 总览

## 概述

数据结构是计算机科学的基础，是组织和存储数据的方式。良好的数据结构设计能够提高算法效率，是程序员面试中的重点考查内容。本章节涵盖了面试中最常见的核心数据结构。

## 📚 章节内容

### 1. [数组 (Arrays)](./arrays.md)
- 线性数据结构基础
- 时间复杂度分析
- 动态数组实现
- 多维数组应用

### 2. [链表 (Linked Lists)](./linked-lists.md)
- 单向链表、双向链表、循环链表
- 链表操作算法
- 经典面试题解析
- 与数组的对比分析

### 3. [栈与队列 (Stacks & Queues)](./stacks-queues.md)
- LIFO和FIFO数据结构
- 栈和队列的实现方式
- 应用场景分析
- 优先队列和双端队列

### 4. [树结构 (Trees)](./trees.md)
- 二叉树基础概念
- 二叉搜索树 (BST)
- 平衡树 (AVL、红黑树)
- B树和B+树
- 树的遍历算法

### 5. [哈希表 (Hash Tables)](./hash-tables.md)
- 哈希函数设计
- 冲突解决策略
- 负载因子与性能
- 一致性哈希

### 6. [堆 (Heaps)](./heaps.md)
- 二叉堆性质
- 堆的构建与维护
- 优先队列应用
- 堆排序算法

### 7. [图结构 (Graphs)](./graphs.md)
- 图的表示方法
- 有向图与无向图
- 图的遍历算法
- 最短路径算法

## 🎯 学习建议

### 学习顺序
1. **基础线性结构**: 数组 → 链表 → 栈和队列
2. **树形结构**: 二叉树 → 二叉搜索树 → 平衡树
3. **哈希结构**: 哈希表的实现与应用
4. **高级结构**: 堆、图结构

### 重点掌握
- **时间复杂度分析**: 各种操作的时间复杂度
- **空间复杂度**: 内存使用效率
- **应用场景**: 何时选择何种数据结构
- **实现细节**: 能够手写基本实现

### 面试重点
- **数组和字符串**: 双指针、滑动窗口
- **链表**: 反转、合并、环检测
- **树**: 遍历、路径问题、公共祖先
- **哈希表**: 快速查找、去重、计数

## 💡 实践建议

### 编程实现
每种数据结构都要能够：
1. 手写基本实现
2. 分析时间空间复杂度
3. 处理边界情况
4. 优化性能

### 题目练习
- **LeetCode标签练习**: 按数据结构分类刷题
- **真题模拟**: 针对性练习高频面试题
- **代码优化**: 追求更简洁高效的实现

## 📖 参考资料

### 经典教材
1. **《算法导论》** - Thomas H. Cormen等
2. **《数据结构与算法分析》** - Mark Allen Weiss
3. **《算法》第4版** - Robert Sedgewick

### 在线资源
1. [Java Collections Framework](https://docs.oracle.com/javase/tutorial/collections/)
2. [Python Data Structures](https://docs.python.org/3/tutorial/datastructures.html)
3. [Visualgo - 数据结构可视化](https://visualgo.net/)
4. [GeeksforGeeks - Data Structures](https://www.geeksforgeeks.org/data-structures/)

### 面试资源
1. [LeetCode](https://leetcode.com/) - 在线练习平台
2. [Coding Interview University](https://github.com/jwasham/coding-interview-university)
3. [System Design Primer](https://github.com/donnemartin/system-design-primer)

---

## 🔗 快速导航

| 数据结构 | 主要操作时间复杂度 | 空间复杂度 | 主要应用场景 |
|---------|-------------------|-----------|-------------|
| [数组](./arrays.md) | 访问O(1), 插入O(n) | O(n) | 随机访问、缓存友好 |
| [链表](./linked-lists.md) | 访问O(n), 插入O(1) | O(n) | 动态大小、频繁插删 |
| [栈](./stacks-queues.md) | 入栈/出栈O(1) | O(n) | 函数调用、表达式求值 |
| [队列](./stacks-queues.md) | 入队/出队O(1) | O(n) | BFS、任务调度 |
| [二叉搜索树](./trees.md) | 搜索O(log n) | O(n) | 有序数据、范围查询 |
| [哈希表](./hash-tables.md) | 查找O(1) | O(n) | 快速查找、键值映射 |
| [堆](./heaps.md) | 插入/删除O(log n) | O(n) | 优先队列、TopK问题 |

通过系统学习这些数据结构，您将具备解决大多数编程面试问题的基础能力。 