# Question

[next-permutation](https://leetcode.cn/problems/next-permutation/)



# Answer

> solution

解决这个问题的思路是基于数学上的“字典序”定义，通过**最右侧的最小改动**来生成下一个更大的排列。它分三步走，每一步都尽量只做必要的工作。

### **`nextPermutation` 问题的解决思路总结**

生成下一个字典序排列的核心思想是**找到尽可能靠右的、能使序列变大的最小改动**。这个改动通常涉及两步：局部交换和局部反转。

具体分解为以下三步：

#### **第一步：找到“下降点” (Pivot)**

*   **目标**：从数组的**右侧**开始向**左**遍历，找到**第一个**元素 `nums[i]`，使得 `nums[i] < nums[i+1]`。
    *   这个 `i` 就是“下降点”或“枢轴”，它是我们为了找到下一个更大的排列而**不得不改变的最靠右的元素**。
    *   例如：`[1, 5, 8, 4, 7, 6, 5, 3, 1]`。从右往左看，`3 < 5`，所以 `i = 7` (索引到 `3`)。不对，`6 > 5`，`7 > 6`，`4 < 7`，所以 `i = 3` (索引到 `4`)。
*   **边界情况**：
    *   如果整个数组都是降序排列（例如 `[3, 2, 1]`），那么就找不到这样的 `i` (即 `i` 会变为 `-1`)。这意味着当前序列已经是**最大排列**。在这种情况下，下一个排列就是最小排列，直接将整个数组**反转**即可（变为 `[1, 2, 3]`）。

#### **第二步：找到“替换点”并交换**

*   **目标**：如果在第一步找到了 `i`（即 `i >= 0`），我们需要在 `nums[i]` 的右侧子序列中（即 `nums[i+1]` 到 `nums[n-1]` 之间），找到**第一个比 `nums[i]` 大的元素 `nums[j]`**。
    *   这个 `j` 应该从数组的**右侧**开始向**左**遍历寻找，以确保它是 `nums[i]` 右侧子序列中“最右边”的、但又“刚刚好比 `nums[i]` 大”的元素。
*   **操作**：交换 `nums[i]` 和 `nums[j]`。
    *   这一步确保了 `nums[i]` 的位置被一个更大的数字替换，从而使整个排列变大。由于我们选择了右侧的最小改动，以及右侧“刚刚好比 `nums[i]` 大”的元素，所以这个改动是最小的。

#### **第三步：反转右侧子序列**

*   **目标**：交换 `nums[i]` 和 `nums[j]` 后，`nums[i]` 右侧的子序列（从 `nums[i+1]` 到 `nums[n-1]`）仍然是降序的（或者说是“无序但仍然不适合作为最小排列的起始”）。为了使新的排列是下一个**最小的**更大排列，这个右侧子序列必须被重新排列成**升序**。
*   **操作**：将 `nums[i+1]` 到 `nums[n-1]` 之间的子序列**反转**。
    *   将一个降序序列反转后，它就变成了升序序列，这正是该子序列在当前情况下能构成的最小排列。

**总结流程图：**

1.  **从右到左找到第一个 `nums[i] < nums[i+1]`。**
    *   如果 `i < 0` (找不到)：整个数组反转。结束。
2.  **从右到左找到第一个 `nums[j] > nums[i]`。**
3.  **交换 `nums[i]` 和 `nums[j]`。**
4.  **反转 `nums[i+1]` 到 `nums[n-1]` 的子序列。**

这个思路保证了每次都能找到紧随当前排列的下一个字典序排列，且时间复杂度为 O(N)，空间复杂度为 O(1)。

### Way Of C

> source code

```c
static inline void swap(int *a, int *b) {
    int tmp = *a;
    *a = *b;
    *b = tmp;
}

static void reverse(int *a, int size) {
    int left = 0;
    int right = size - 1;
    while (left < right) {
        swap(a + left, a + right);
        ++left;
        --right;
    }
}

void nextPermutation(int* nums, int numsSize) {
    if (numsSize <= 1) return;
    int i = numsSize - 2;
    while (i >= 0 && nums[i] >= nums[i + 1]) --i;
    if (i >= 0) {
        int j = numsSize - 1;
        while (j >= 0 && nums[j] <= nums[i]) --j;
        swap(nums + i, nums + j);
    }
    reverse(nums + i + 1, numsSize - (i + 1));
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int n = nums.size();
        if (n <= 1) return;

        int i = n - 2;
        while (i >= 0 && nums[i] >= nums[i + 1]) --i;

        if (i >= 0) {
            int j = n - 1;
            while (j >= 0 && nums[j] <= nums[i]) --j;
            std::swap(nums[i], nums[j]);
        }
        std::reverse(nums.begin() + i + 1, nums.end());
    }
};
```
