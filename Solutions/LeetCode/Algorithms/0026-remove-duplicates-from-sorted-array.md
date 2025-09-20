# Question

[remove-duplicates-from-sorted-array](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)



# Answer

> solution

解决“从有序数组中移除重复项”问题的核心思路是**双指针法**，也常被称为**快慢指针法**。由于数组是有序的，所有重复项都会紧挨着出现，这使得我们可以高效地进行原地修改。

### **核心算法思路 (C++ 和 C 共享)**

1.  **处理边界情况**：
    *   如果输入的数组为空 (`numsSize == 0` 或 `nums.empty()`)，那么没有元素可以处理，直接返回 `0` 作为新长度。

2.  **定义双指针**：
    *   **慢指针 (`slow_ptr` 或 `k`)**：
        *   **作用**：指向“处理后”的数组中**下一个非重复元素应该写入的位置**。它同时也是当前非重复元素的**数量**，或者说最终新数组的**长度**。
        *   **初始化**：通常从 `0` 开始。
    *   **快指针 (`fast_ptr` 或 `i`)**：
        *   **作用**：遍历整个原始数组，寻找非重复元素。
        *   **初始化**：通常从 `0` 或 `1` 开始（如果第一个元素总是保留）。

3.  **遍历与比较**：
    *   使用快指针 `fast_ptr` 从头到尾遍历数组。
    *   **判断条件**：对于 `nums[fast_ptr]`，我们判断它是否应该被保留（即它是否是一个新的、未曾添加到结果中的非重复元素）。判断逻辑是：
        *   如果 `slow_ptr` 是 `0` (意味着我们正在处理第一个元素)，那么 `nums[fast_ptr]` 总是要被保留的。
        *   否则 (`slow_ptr > 0`)，如果 `nums[fast_ptr]` **不等于** `nums[slow_ptr - 1]` (即，不等于上一个已写入的非重复元素)，那么 `nums[fast_ptr]` 是一个新的非重复元素，应该被保留。
    *   **为什么要比较 `nums[fast_ptr]` 和 `nums[slow_ptr - 1]`？** `slow_ptr - 1` 指向的是**上一个已写入的非重复元素**。因为数组是有序的，我们只需要和上一个写入的元素比较，就能知道当前元素是否是重复的。

4.  **原地写入与更新慢指针**：
    *   如果 `nums[fast_ptr]` 符合保留条件（即它是一个新的非重复元素）：
        1.  将 `nums[fast_ptr]` 的值复制到 `nums[slow_ptr]`。(`nums[slow_ptr] = nums[fast_ptr];`)
        2.  将 `slow_ptr` 向前移动一位，准备接收下一个非重复元素。(`slow_ptr++;`)

5.  **返回结果**：
    *   循环结束后，`slow_ptr` 的值就是数组中非重复元素的数量，也就是新数组的最终长度。返回 `slow_ptr`。

### **C++ 版本和 C 版本在实现细节上的核心差异**

核心算法逻辑是完全相同的，两者都体现了双指针原地修改的精髓。差异主要在于语言特有的数据结构和语法：

#### **C++ 版本解决问题的核心**

*   **`std::vector<int>`**：C++ 版本利用 `std::vector` 提供的动态数组功能，但在这个问题中，它仍被当作固定大小的数组在原地修改。`nums.empty()` 和 `nums.size()` 提供了方便的容器操作。
*   **类封装**：`class Solution { public: ... };` 结构是 C++ 中解决 LeetCode 这类问题的标准范式。

#### **C 版本解决问题的核心**

*   **原始数组和指针**：C 版本直接使用 `int* nums` 和 `int numsSize`，更贴近底层内存操作。
*   **手动边界检查**：对于数组大小为 0 的情况，需要显式检查 `numsSize == 0`。
*   **Linus 哲学**：在 C 中，一切都直接操作内存和指针。这种“快慢指针”模式正是 C 语言处理数组和序列的典型高效方法，它避免了不必要的内存分配和复制，直接在原地修改，符合“简洁执念”和“实用主义”。

**总而言之，无论 C++ 还是 C，解决此问题的核心都是利用数组的有序性，采用双指针（快慢指针）技术在原地进行修改。慢指针负责指示下一个非重复元素的写入位置和最终长度，快指针负责遍历数组寻找新的非重复元素，并与慢指针的前一个位置进行比较。**

### Way Of C

> source code

```c
int removeDuplicates(int* nums, int numsSize) {
    if (numsSize == 0) return 0;

    int slow_ptr = 0;

    for (int fast_ptr = 0; fast_ptr < numsSize; ++fast_ptr) {
        if (slow_ptr == 0 || nums[fast_ptr] != nums[slow_ptr - 1]) {
            nums[slow_ptr] = nums[fast_ptr];
            ++slow_ptr;
        }
    }

    return slow_ptr;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if (nums.empty()) return 0;

        int slow_ptr = 0;

        for (int fast_ptr = 0; fast_ptr < nums.size(); ++fast_ptr) {
            if (slow_ptr == 0 || nums[fast_ptr] != nums[slow_ptr - 1]) {
                nums[slow_ptr] = nums[fast_ptr];
                ++slow_ptr;
            }
        }

        return slow_ptr;
    }
};
```
