# Question

[merge-sorted-array](https://leetcode.cn/problems/merge-sorted-array/)



# Answer

> solution

### 双指针，从后向前填充

无论是 C 还是 C++ 版本，都采用了相同的**“三指针逆向合并”**策略。

#### 1. 指针定义

设置三个指针：

| 指针 | 名称 | 目的 |
| :--- | :--- | :--- |
| **`i`** | `nums1` 有效数据指针 | 指向 `nums1` 中最后一个有效元素（索引 $m-1$）。 |
| **`j`** | `nums2` 数据指针 | 指向 `nums2` 中最后一个有效元素（索引 $n-1$）。 |
| **`k`** | **目标填充指针** | 指向合并后数组的末尾（索引 $m+n-1$）。 |

#### 2. 核心比较和移动

使用一个 `while (i >= 0 && j >= 0)` 循环，在两个数组都有剩余元素时进行比较：

*   在 `nums1[i]` 和 `nums2[j]` 中选择**较大**的元素。
*   将较大元素放入 `nums1[k]` 的位置。
*   将对应的源指针 (`i` 或 `j`) 和目标指针 (`k`) **同时向左移动**（递减）。

这保证了最大的元素首先被放置在目标数组的末尾，且不会覆盖掉任何尚未比较的元素。

#### 3. 扫尾逻辑（特殊情况处理）

当核心循环结束后，至少有一个源数组被清空：

*   **如果 `nums1` 还有剩余（$i \ge 0$）**：这些元素本身就是数组中最小的元素，且已经处于 `nums1` 的正确头部位置。**无需移动**。这是该算法的优雅之处。
*   **如果 `nums2` 还有剩余（$j \ge 0$）**：这意味着 `nums2` 中剩余的元素都比 `nums1` 中所有未处理的元素小。需要使用第二个 `while (j >= 0)` 循环，将这些剩余的元素直接复制到 `nums1` 的头部。

### Way Of C

> source code

```c
/**
 * C语言的实用主义实现：从后向前合并，避免元素覆盖。
 * 
 * @param nums1     目标数组，前 m 个是有效数据，总大小为 nums1Size。
 * @param nums1Size nums1 的实际总大小 (m + n)。
 * @param m         nums1 中有效元素的数量。
 * @param nums2     源数组。
 * @param nums2Size nums2 的总大小 (未使用，但包含在签名中)。
 * @param n         nums2 中有效元素的数量。
 */
void merge(int* nums1, int nums1Size, int m, int* nums2, int nums2Size, int n) 
{
    // i: nums1 有效数据的指针 (从后向前)
    int i = m - 1;
    // j: nums2 有效数据的指针 (从后向前)
    int j = n - 1;
    // k: nums1 填充位置的指针 (从后向前)，使用 nums1Size 保证精确性。
    int k = nums1Size - 1; 

    // 核心循环：当两个数组都有剩余元素时进行比较
    while (i >= 0 && j >= 0) {
        // 哪个元素大，哪个就先被放入 nums1 的尾部。
        // 如果相等，优先移动 nums1 的元素（保持稳定性和简洁性）。
        if (nums1[i] >= nums2[j]) {
            nums1[k--] = nums1[i--];
        } else {
            nums1[k--] = nums2[j--];
        }
    }

    // 扫尾：如果 nums2 还有剩余元素，需要全部复制到 nums1 的头部。
    // 如果 nums1 还有剩余元素 (i >= 0)，它们本身就在正确位置，不需要额外处理。
    while (j >= 0) {
        nums1[k--] = nums2[j--];
    }
    
    // 思考：如果 i 还有剩余，它们已经在 nums1[0..i] 的位置上，
    // 且 k 此时一定等于 i。所以无需额外的 while (i >= 0) 循环。
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int i = m - 1; // nums1的有效起始点
        int j = n - 1; // nums2的起始点
        int k = nums1.size() - 1; // 填充目标点

        while (i >= 0 && j >= 0) {
            // 注意：两种比较方式都可以，但逻辑要保持一致。
            // 选哪个大，就把哪个放入 k
            if (nums1[i] < nums2[j]) { 
                nums1[k--] = nums2[j--];
            } else {
                nums1[k--] = nums1[i--];
            }
        }
        
        // 扫尾：只处理 nums2 剩余元素。
        // nums1 剩余元素已经在原地，无需移动。这是关键的简洁之处。
        while (j >= 0) {
            nums1[k--] = nums2[j--];
        }
    }
};
```
