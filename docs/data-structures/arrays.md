# 数组 (Arrays)

## 定义与基本概念

数组是一种线性数据结构，用于存储相同类型的元素集合。数组中的元素在内存中连续存储，通过索引来访问特定位置的元素。

### 关键特性

1. **连续内存存储**: 数组元素在内存中按顺序连续排列
2. **固定大小**: 传统数组在创建时确定大小，无法动态改变
3. **随机访问**: 通过索引可以在O(1)时间内访问任意元素
4. **同质性**: 所有元素必须是相同的数据类型

## 时间复杂度分析

| 操作 | 时间复杂度 | 说明 |
|------|-----------|------|
| 访问 | O(1) | 通过索引直接访问 |
| 搜索 | O(n) | 需要遍历数组 |
| 插入 | O(n) | 需要移动后续元素 |
| 删除 | O(n) | 需要移动后续元素 |
| 修改 | O(1) | 直接通过索引修改 |

**空间复杂度**: O(n)，n为数组元素个数

## 基本实现

### 静态数组

```java
public class StaticArray {
    private int[] array;
    private int size;
    
    public StaticArray(int capacity) {
        this.array = new int[capacity];
        this.size = 0;
    }
    
    // 访问元素
    public int get(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index);
        }
        return array[index];
    }
    
    // 修改元素
    public void set(int index, int value) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index);
        }
        array[index] = value;
    }
    
    // 在末尾添加元素
    public void add(int value) {
        if (size >= array.length) {
            throw new RuntimeException("Array is full");
        }
        array[size++] = value;
    }
    
    // 在指定位置插入元素
    public void insert(int index, int value) {
        if (index < 0 || index > size) {
            throw new IndexOutOfBoundsException("Index: " + index);
        }
        if (size >= array.length) {
            throw new RuntimeException("Array is full");
        }
        
        // 向后移动元素
        for (int i = size; i > index; i--) {
            array[i] = array[i - 1];
        }
        
        array[index] = value;
        size++;
    }
    
    // 删除指定位置的元素
    public int remove(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index);
        }
        
        int removedValue = array[index];
        
        // 向前移动元素
        for (int i = index; i < size - 1; i++) {
            array[i] = array[i + 1];
        }
        
        size--;
        return removedValue;
    }
    
    public int size() {
        return size;
    }
    
    public boolean isEmpty() {
        return size == 0;
    }
}
```

### 动态数组 (Dynamic Array)

```java
public class DynamicArray<T> {
    private Object[] array;
    private int size;
    private int capacity;
    private static final int DEFAULT_CAPACITY = 10;
    
    public DynamicArray() {
        this.capacity = DEFAULT_CAPACITY;
        this.array = new Object[capacity];
        this.size = 0;
    }
    
    public DynamicArray(int initialCapacity) {
        this.capacity = initialCapacity;
        this.array = new Object[capacity];
        this.size = 0;
    }
    
    @SuppressWarnings("unchecked")
    public T get(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index);
        }
        return (T) array[index];
    }
    
    public void set(int index, T value) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index);
        }
        array[index] = value;
    }
    
    public void add(T value) {
        ensureCapacity();
        array[size++] = value;
    }
    
    public void insert(int index, T value) {
        if (index < 0 || index > size) {
            throw new IndexOutOfBoundsException("Index: " + index);
        }
        
        ensureCapacity();
        
        // 向后移动元素
        System.arraycopy(array, index, array, index + 1, size - index);
        
        array[index] = value;
        size++;
    }
    
    @SuppressWarnings("unchecked")
    public T remove(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index);
        }
        
        T removedValue = (T) array[index];
        
        // 向前移动元素
        System.arraycopy(array, index + 1, array, index, size - index - 1);
        
        array[--size] = null; // 清除引用
        
        // 缩容检查
        if (size > 0 && size == capacity / 4) {
            resize(capacity / 2);
        }
        
        return removedValue;
    }
    
    private void ensureCapacity() {
        if (size >= capacity) {
            resize(capacity * 2);
        }
    }
    
    private void resize(int newCapacity) {
        Object[] newArray = new Object[newCapacity];
        System.arraycopy(array, 0, newArray, 0, size);
        array = newArray;
        capacity = newCapacity;
    }
    
    public int size() {
        return size;
    }
    
    public boolean isEmpty() {
        return size == 0;
    }
    
    public int capacity() {
        return capacity;
    }
}
```

## 常见算法

### 1. 线性搜索

```java
public class ArraySearch {
    // 线性搜索
    public static int linearSearch(int[] arr, int target) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == target) {
                return i;
            }
        }
        return -1;
    }
    
    // 二分搜索（数组必须有序）
    public static int binarySearch(int[] arr, int target) {
        int left = 0, right = arr.length - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            
            if (arr[mid] == target) {
                return mid;
            } else if (arr[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return -1;
    }
}
```

### 2. 数组反转

```java
public class ArrayReverse {
    // 反转整个数组
    public static void reverse(int[] arr) {
        int left = 0, right = arr.length - 1;
        
        while (left < right) {
            int temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
            left++;
            right--;
        }
    }
    
    // 反转数组的一部分
    public static void reverse(int[] arr, int start, int end) {
        while (start < end) {
            int temp = arr[start];
            arr[start] = arr[end];
            arr[end] = temp;
            start++;
            end--;
        }
    }
}
```

### 3. 数组旋转

```java
public class ArrayRotation {
    // 左旋转数组k位
    public static void rotateLeft(int[] arr, int k) {
        int n = arr.length;
        k = k % n; // 处理k大于数组长度的情况
        
        reverse(arr, 0, k - 1);      // 反转前k个元素
        reverse(arr, k, n - 1);      // 反转剩余元素
        reverse(arr, 0, n - 1);      // 反转整个数组
    }
    
    // 右旋转数组k位
    public static void rotateRight(int[] arr, int k) {
        int n = arr.length;
        k = k % n;
        
        reverse(arr, 0, n - 1);      // 反转整个数组
        reverse(arr, 0, k - 1);      // 反转前k个元素
        reverse(arr, k, n - 1);      // 反转剩余元素
    }
    
    private static void reverse(int[] arr, int start, int end) {
        while (start < end) {
            int temp = arr[start];
            arr[start] = arr[end];
            arr[end] = temp;
            start++;
            end--;
        }
    }
}
```

## 经典面试题

### 1. 两数之和 (Two Sum)

```java
import java.util.*;

public class TwoSum {
    // 暴力解法：O(n²)时间，O(1)空间
    public int[] twoSumBruteForce(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    return new int[]{i, j};
                }
            }
        }
        return new int[0];
    }
    
    // 哈希表解法：O(n)时间，O(n)空间
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)) {
                return new int[]{map.get(complement), i};
            }
            map.put(nums[i], i);
        }
        
        return new int[0];
    }
}
```

### 2. 移除元素

```java
public class RemoveElement {
    // 移除所有值等于val的元素
    public int removeElement(int[] nums, int val) {
        int writeIndex = 0;
        
        for (int readIndex = 0; readIndex < nums.length; readIndex++) {
            if (nums[readIndex] != val) {
                nums[writeIndex] = nums[readIndex];
                writeIndex++;
            }
        }
        
        return writeIndex;
    }
    
    // 移除重复元素（有序数组）
    public int removeDuplicates(int[] nums) {
        if (nums.length <= 1) {
            return nums.length;
        }
        
        int writeIndex = 1;
        
        for (int readIndex = 1; readIndex < nums.length; readIndex++) {
            if (nums[readIndex] != nums[readIndex - 1]) {
                nums[writeIndex] = nums[readIndex];
                writeIndex++;
            }
        }
        
        return writeIndex;
    }
}
```

### 3. 最大子数组和 (Kadane's Algorithm)

```java
public class MaxSubarray {
    // Kadane算法：O(n)时间，O(1)空间
    public int maxSubArray(int[] nums) {
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

### 4. 双指针技巧

```java
public class TwoPointers {
    // 判断是否为回文
    public boolean isPalindrome(char[] chars) {
        int left = 0, right = chars.length - 1;
        
        while (left < right) {
            if (chars[left] != chars[right]) {
                return false;
            }
            left++;
            right--;
        }
        
        return true;
    }
    
    // 三数之和为0
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums);
        
        for (int i = 0; i < nums.length - 2; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue; // 跳过重复
            
            int left = i + 1, right = nums.length - 1;
            
            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];
                
                if (sum == 0) {
                    result.add(Arrays.asList(nums[i], nums[left], nums[right]));
                    
                    while (left < right && nums[left] == nums[left + 1]) left++;
                    while (left < right && nums[right] == nums[right - 1]) right--;
                    
                    left++;
                    right--;
                } else if (sum < 0) {
                    left++;
                } else {
                    right--;
                }
            }
        }
        
        return result;
    }
}
```

## 多维数组

### 二维数组

```java
public class Matrix {
    // 矩阵转置
    public int[][] transpose(int[][] matrix) {
        int rows = matrix.length;
        int cols = matrix[0].length;
        int[][] transposed = new int[cols][rows];
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                transposed[j][i] = matrix[i][j];
            }
        }
        
        return transposed;
    }
    
    // 螺旋遍历矩阵
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> result = new ArrayList<>();
        if (matrix.length == 0) return result;
        
        int top = 0, bottom = matrix.length - 1;
        int left = 0, right = matrix[0].length - 1;
        
        while (top <= bottom && left <= right) {
            // 向右
            for (int j = left; j <= right; j++) {
                result.add(matrix[top][j]);
            }
            top++;
            
            // 向下
            for (int i = top; i <= bottom; i++) {
                result.add(matrix[i][right]);
            }
            right--;
            
            // 向左
            if (top <= bottom) {
                for (int j = right; j >= left; j--) {
                    result.add(matrix[bottom][j]);
                }
                bottom--;
            }
            
            // 向上
            if (left <= right) {
                for (int i = bottom; i >= top; i--) {
                    result.add(matrix[i][left]);
                }
                left++;
            }
        }
        
        return result;
    }
}
```

## 数组与其他数据结构的对比

| 特性 | 数组 | 链表 | 动态数组 |
|------|------|------|----------|
| 内存布局 | 连续 | 非连续 | 连续 |
| 访问时间 | O(1) | O(n) | O(1) |
| 插入/删除 | O(n) | O(1) | 摊销O(1) |
| 内存开销 | 低 | 高（指针） | 中等 |
| 缓存友好性 | 好 | 差 | 好 |

## 优化技巧

### 1. 预分配容量
```java
// 避免频繁扩容
List<Integer> list = new ArrayList<>(expectedSize);
```

### 2. 批量操作
```java
// 使用System.arraycopy进行批量复制
System.arraycopy(src, srcPos, dest, destPos, length);
```

### 3. 原地算法
```java
// 尽可能原地修改，减少空间复杂度
public void sortColors(int[] nums) {
    int red = 0, white = 0, blue = nums.length - 1;
    
    while (white <= blue) {
        if (nums[white] == 0) {
            swap(nums, red++, white++);
        } else if (nums[white] == 1) {
            white++;
        } else {
            swap(nums, white, blue--);
        }
    }
}
```

## 面试准备要点

### 必须掌握
1. **基本操作**: 访问、插入、删除、搜索
2. **双指针技巧**: 快慢指针、首尾指针
3. **滑动窗口**: 固定/可变窗口大小
4. **前缀和**: 区间求和优化
5. **原地算法**: 空间优化技巧

### 常见陷阱
1. **数组越界**: 注意边界检查
2. **整数溢出**: 大数运算要小心
3. **空数组处理**: 特殊情况判断
4. **重复元素**: 去重逻辑

### 优化思路
1. **时间优化**: 排序预处理、哈希表加速
2. **空间优化**: 原地算法、双指针
3. **代码简洁**: 库函数使用、边界统一

## 参考资料

1. **《算法导论》第6章** - 数组和基本数据结构
2. **《编程珠玑》** - 数组算法优化技巧
3. [Java Arrays Documentation](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html)
4. [Python List Documentation](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)
5. [LeetCode Array Problems](https://leetcode.com/tag/array/)

---

**下一章**: [链表 (Linked Lists)](./linked-lists.md) 