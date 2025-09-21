# Question

[remove-element](https://leetcode.cn/problems/remove-element/)



# Answer

> solution

解决“从数组中移除指定元素”问题的核心思路是**双指针法**，也常被称为**快慢指针法**。这种方法可以在**原地**修改数组，同时避免了不必要的元素移动和额外的空间开销。

### **核心算法思路 (C++ 和 C 共享)**

1.  **定义双指针**：
    *   **慢指针 (`slow_ptr` 或 `count` / `k`)**：
        *   **作用**：指向“处理后”的数组中**下一个非指定元素 `val` 应该写入的位置**。它同时也是当前已写入的非 `val` 元素的数量，即最终新数组的**长度**。
        *   **初始化**：从 `0` 开始。
    *   **快指针 (`fast_ptr` 或 `i`)**：
        *   **作用**：遍历整个原始数组，逐个检查每个元素。
        *   **初始化**：从 `0` 开始。

2.  **遍历与条件判断**：
    *   使用快指针 `fast_ptr` 从头到尾遍历数组中的所有元素。
    *   对于 `nums[fast_ptr]`，我们判断它是否应该被保留（即它是否不等于 `val`）。
    *   **判断条件**：`if (nums[fast_ptr] != val)`

3.  **原地写入与更新慢指针**：
    *   如果 `nums[fast_ptr]` **不等于** `val` (即它是一个应该被保留的元素)：
        1.  将 `nums[fast_ptr]` 的值复制到 `nums[slow_ptr]`。(`nums[slow_ptr] = nums[fast_ptr];`)
        2.  将 `slow_ptr` 向前移动一位，准备接收下一个非 `val` 元素。(`slow_ptr++;`)
    *   如果 `nums[fast_ptr]` **等于** `val`，则什么也不做，快指针继续向前移动，相当于“跳过”这个元素。

4.  **返回结果**：
    *   快指针遍历完整个数组后，`slow_ptr` 的值就代表了数组中所有非 `val` 元素的数量。这个值就是数组的新长度。返回 `slow_ptr`。

### **C++ 版本和 C 版本在实现细节上的核心差异**

核心算法逻辑是完全相同的，两者都体现了双指针原地修改的精髓。差异主要在于语言特有的数据结构和语法：

#### **C++ 版本解决问题的核心**

*   **`std::vector<int>`**：C++ 版本利用 `std::vector` 作为动态数组，提供了 `.size()` 方法来获取数组当前长度，以及更简洁的语法糖。但在这个问题中，它仍被当作一个可原地修改的底层数组。
*   **类封装**：`class Solution { public: ... };` 结构是 C++ 中解决 LeetCode 这类问题的标准范式。

#### **C 版本解决问题的核心**

*   **原始数组和指针**：C 版本直接使用 `int* nums` 和 `int numsSize`，更贴近底层内存操作。
*   **手动传递大小**：`numsSize` 参数必须显式传递，因为 C 数组不包含其自身的长度信息。
*   **Linus 哲学**：在 C 中，一切都直接操作内存和指针。这种“快慢指针”模式正是 C 语言处理数组和序列的典型高效方法。它通过最小的代价（O(1) 额外空间）完成了数据的筛选和重排，符合“简洁执念”和“实用主义”的原则。它避免了不必要的内存分配和复制，直接在原地修改数据。

**总而言之，无论 C++ 还是 C，解决此问题的核心都是利用双指针（快慢指针）技术在原地修改数组。快指针遍历所有元素，慢指针记录非目标元素的新位置，并将非目标元素复制到慢指针指向的位置，从而实现“移除”效果，最终返回慢指针的最终位置作为新长度。**

### Way Of C

> source code

```c
int removeElement(int* nums, int numsSize, int val) {
    int slow_ptr = 0;
    for (int fast_ptr = 0; fast_ptr < numsSize; ++fast_ptr) {
        if (nums[fast_ptr] != val) {
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
    int removeElement(vector<int>& nums, int val) {
        int slow_ptr = 0;
        for (int fast_ptr = 0; fast_ptr < nums.size(); ++fast_ptr) {
            if (nums[fast_ptr] != val) {
                nums[slow_ptr] = nums[fast_ptr];
                ++slow_ptr;
            }
        }
        return slow_ptr;
    }
};
```
