# Question

[remove-duplicates-from-sorted-array-ii](https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii/)



# Answer

> solution

删除重复项 II

### 1. 核心挑战与策略

*   **挑战**：在单次遍历中，高效地判断当前元素是否已经出现两次或更多次。
*   **策略**：**双指针 (读写分离) + 逆向判断**。

### 2. 算法核心：双指针

我们使用两个逻辑指针进行一次线性扫描：

*   **写指针 $k$**：表示已处理数组的有效长度，同时指向下一个要写入的位置。
*   **读指针 $i$**（隐式或显式）：遍历原始数组中的所有元素。

### 3. 关键：逆向判断的简洁性

解决这个问题的关键在于避免使用额外的变量（如 `count`）来跟踪当前数字的重复次数。我们只需要查看**已写入结果**的末尾状态。

假设我们已经将数组的前 $k$ 个元素处理完毕，下一个元素 $x$ 是否可以写入到 $nums[k]$？

1.  **基础条件**：如果 $k < 2$，那么 $x$ 之前最多只写入了一个元素，所以 $x$ 无条件允许写入。
2.  **核心判断**：如果 $k \ge 2$，我们只需要检查 $x$ 是否与**倒数第二个已写入的元素** $nums[k-2]$ 相同。
    *   **如果 $x = nums[k-2]$**：由于数组是有序的，那么 $nums[k-1]$ 必然也等于 $x$。这意味着 $x$ 已经连续出现了两次，我们必须跳过它。
    *   **如果 $x \ne nums[k-2]$**：这意味着 $x$ 在 $k-2$ 处尚未出现过，因此允许写入。

### 4. 最终判断逻辑

将上述逻辑合并为一个简洁的布尔表达式：

$$\text{允许写入 } \Leftrightarrow k < 2 \quad \text{ OR } \quad nums[k-2] \ne x$$

这种策略将判断逻辑压缩到 $O(1)$ 时间，且避免了额外的状态变量，是 $O(1)$ 空间复杂度的**好品味**实现。

### Way Of C

> source code

```c
/**
 * 删除有序数组中的重复项 II (最多两次)
 * O(N) 时间，O(1) 空间
 */
int removeDuplicates(int* nums, int numsSize)
{
    if (numsSize == 0) {
        return 0;
    }

    // k 是写指针，指示下一个要放置元素的位置
    int k = 0;

    // i 是读指针
    for (int i = 0; i < numsSize; i++) {
        int x = nums[i]; // 当前读取的元素

        // 写入条件：
        // 1. k < 2 (前两个元素总是允许)
        // OR
        // 2. 当前元素 x 不等于倒数第二个已写入的元素 (nums[k - 2])
        //    (这确保了 nums[k-2], nums[k-1] 和 x 不会形成三联重复)
        
        if (k < 2 || nums[k - 2] != x) {
            nums[k] = x;
            k++;
        }
    }

    return k;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        // k 是写指针，指示下一个要写入的位置
        int k = 0;
        
        // x 是读指针，遍历数组中的所有元素
        for (int x : nums) {
            
            // 写入条件：
            // 1. k < 2 (前两个元素总是允许的)
            // OR
            // 2. 当前元素 x 不等于倒数第二个已写入的元素 (nums[k - 2])
            // NOTE: 因为数组是有序的，如果 nums[k-1] != x，那么 nums[k-2] 必然 != x (或它根本不存在)
            // 我们可以只检查 k-2 是否不同，因为如果 k-2 == x，则 k-1 也必然 == x (否则数组不是有序的)
            
            if (k < 2 || nums[k - 2] != x) {
                nums[k++] = x;
            }
        }
        return k;
    }
};
```
