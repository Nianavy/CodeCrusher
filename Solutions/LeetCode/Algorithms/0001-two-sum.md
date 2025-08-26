# Question

[Two Sum](https://leetcode.cn/problems/two-sum/)



# Answer

### Way Of C

> think

1.  **保存原始索引 (Original Index Preservation):**
    *   **问题：** `twoSum` 最终要返回的不是数字本身，而是它们在**原始数组**中的**索引**。但如果直接对原始数组排序，索引信息就丢失了。
    *   **关键：** 创建了一个`object`结构体，把每个数字的`val`和它在`nums`数组中的`index`**绑定**在一起。
    *   **重要性：** 这确保了无论`object`数组如何被排序，每个数字的原始位置信息都紧随其后，不会丢失。这是后续所有操作的前提。

2.  **排序 (Sorting) - 为高效查找创造条件：**
    *   **问题：** 在一个无序数组中找两个数之和，最暴力的方法是 `O(N^2)`（双重循环遍历所有对）。
    *   **关键：** 使用 `qsort` 对`objs`数组（也就是`val`和`index`绑定的结构体）按照`val`字段进行升序排序。
    *   **重要性：** 排序的目的是把所有**可能的配对**从随机位置拉到**相对有序的位置**。一旦数组有序，我们就能利用这种顺序性进行**高效的、线性的查找**。

3.  **双指针 (Two Pointers) - O(N) 线性查找：**
    *   **问题：** 排序后，如何以比 `O(N^2)` 更快的速度找到目标和？
    *   **关键：** 设置了两个指针，`i` 从排序后数组的头部开始（指向最小的数），`j` 从尾部开始（指向最大的数）。
    *   **重要性及逻辑：**
        *   **`sum = objs[i].val + objs[j].val;`**：计算当前 `i` 和 `j` 指向的两个数的和。
        *   **`if (sum < target) ++i;`**：如果当前和小于目标值，说明需要更大的和。由于 `j` 已经指向最大（或相对较大）的数，再减小 `j` 只会使和更小。所以，我们只能通过**增大 `i`**（指向下一个更大的数）来尝试增大和。
        *   **`else if (sum > target) --j;`**：如果当前和大于目标值，说明需要更小的和。同理，增大 `i` 只会使和更大。所以，我们只能通过**减小 `j`**（指向下一个更小的数）来尝试减小和。
        *   **`else { /* Found it! */ }`**：如果 `sum == target`，找到了！直接取出 `objs[i].index` 和 `objs[j].index` 作为结果。
        *   **`while (i < j)`**：这个循环保证了指针 `i` 不会越过 `j`，避免重复和无效的比较。
    *   **效率：** 由于 `i` 只会向前移动，`j` 只会向后移动，每个元素最多被两个指针访问一次。所以，双指针的查找阶段是**线性的 `O(N)`**。

**综合起来，解决问题的关键在于：**
先通过 `O(N)` 的预处理（封装 `object` 结构体），然后用 `O(N log N)` 的排序打破原始数组的无序性，最后利用排序后的特性，通过 `O(N)` 的双指针法，快速地在`O(N log N)` 的总时间复杂度内找到目标和，并返回正确的原始索引。

这种“先转化，再排序，后线性扫描”的模式，是解决许多数组查找问题的经典高效策略。

> source code

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct object {
    int val;
    int index;
}object;

static int compare(const void *a, const void *b) {
    return ((object*)a)->val - ((object*)b)->val;
}
int *twoSum(int *, int, int, int *);

int main() {
    int nums[] = {9, 5, 9};
    int size = sizeof(nums) / sizeof(*nums);
    int target = 18;
    int cnt = 0;
    int *indexes = twoSum(nums, size, target, &cnt);
    if (indexes) printf("%d %d\n", indexes[0], indexes[1]);
    else printf("Not found\n");
    return 0;
}


int* twoSum(int* nums, int numsSize, int target, int* returnSize) {
    int i, j;
    object *objs = malloc(sizeof(object) * numsSize);
    for (i = 0; i < numsSize; ++i) {
        objs[i].val = nums[i];
        objs[i].index = i;
    }
    qsort(objs, numsSize, sizeof(object), compare);
    int *results = malloc(sizeof(int) * 2);
    i = 0, j = numsSize - 1;
    while (i < j) {
        int sum = objs[i].val + objs[j].val;
        if (sum < target) ++i;
        else if (sum > target) --j;
        else {
            results[0] = objs[i].index;
            results[1] = objs[j].index;
            *returnSize = 2;
            free(objs);
            return results;
        }
    }
    free(objs);
    return NULL;
}
```

---


### Way Of C++

> think

1.  **哈希表 (Hash Table / `unordered_map`) - O(1) 平均查找的魔法：**
    *   **问题：** 在数组中找一个数 `x` 的“另一半” `(target - x)`，传统方法在无序数组中是 `O(N)` 扫描，在有序数组中是 `O(log N)` 二分查找。
    *   **关键：** 使用了 `std::unordered_map`。这个数据结构的**核心特性**就是，给定一个键（Key），它能以**平均 `O(1)` 的时间复杂度**（非常快，几乎是常数时间）查找这个键是否存在，并获取其对应的值。
    *   **重要性：** 哈希表在这里扮演了一个**“快速记忆器”**的角色，能够瞬间判断一个数字是否曾经出现过，以及它在数组中的位置。

2.  **一次遍历 (Single Pass) - 边找边存：**
    *   **问题：** 传统的查找问题常常需要多次遍历或嵌套循环。
    *   **关键：** 代码只遍历了 `nums` 数组**一次**。
    *   **重要性：** 在这次遍历中，对于 `nums[i]`，它同时扮演了两个角色：
        *   **查找者：** 尝试在哈希表中找到自己的“另一半” `(target - nums[i])`。
        *   **被查找者：** 如果没找到自己的“另一半”，就把自己和自己的索引存入哈希表，为后续数字查找自己做准备。
    *   这种“边走边看，边看边存”的策略，是实现 `O(N)` 时间复杂度的基石。

3.  **查找“另一半”的巧妙逻辑 (Complementary Search):**
    *   **问题：** 如何判断 `nums[i]` 是否能与之前某个数字凑成 `target`？
    *   **关键：** 对于当前遍历到的 `nums[i]`，计算出它需要的**互补数** `other = target - nums[i]`。
    *   **重要性及逻辑：**
        *   **`if (ht.count(other))`**：在哈希表中快速查询 `other` 是否已经存在。如果存在，就意味着在 `nums[i]` 之前已经有一个 `ht[other]` 这个数字，并且 `ht[other]` 存储了它的**原始索引**。此时，`nums[i]` 和 `nums[ht[other]]` 就是我们找的两个数，它们的原始索引是 `ht[other]` 和 `i`。
        *   **`ht[nums[i]] = i;`**：如果 `other` 不存在（或者 `nums[i]` 自己就是 `other` 但为了避免自身匹配），那么就将当前 `nums[i]` 和它的索引 `i` 存入哈希表。这样，后续遍历到的数字就可以查找 `nums[i]` 了。
    *   **索引的保存：** 哈希表的**值**（Value）部分存储的是数字的原始索引。这是为了最终返回正确的原始位置，而不是数字本身。

**综合起来，解决问题的关键在于：**
利用 `std::unordered_map` 平均 `O(1)` 的查找和插入特性，在**一次线性遍历**中，对于每个数字，既**查找**它所需要的互补数，又将自己**存储**起来以备后续查找，从而将总时间复杂度降至**平均 `O(N)`**。这是在牺牲 `O(N)` 额外空间（用于哈希表）的情况下，换取时间效率最大化的经典权衡。

> source code
```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> res;
        unordered_map<int, int> ht;
        for (int i = 0; i < nums.size(); ++i) {
            int other = target - nums[i];
            if (ht.count(other)) {
                res.push_back(ht[other]);
                res.push_back(i);
                return res;
            }
            ht[nums[i]] = i;
        }
        return res;
    }
};
```
