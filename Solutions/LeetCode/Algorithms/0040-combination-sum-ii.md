# Question

[combination-sum-ii](https://leetcode.cn/problems/combination-sum-ii/)



# Answer

> solution

解决这个问题的两种基本思路都是**回溯（Backtracking）**。它们的核心都是通过深度优先搜索来探索所有可能的组合路径，但都增加了去重逻辑来确保最终的组合是唯一的。主要的区别在于如何处理候选集中重复的数字。

### **`combinationSum2` 问题的两种解决思路总结**

`combinationSum2` 问题要求从给定的候选数字集中找出所有和为目标值的**唯一组合**。每个数字在**一个组合中只能使用一次**，但候选数字集 `candidates` 本身可能包含重复项。

---

#### **思路一：基于“排序 + 遍历跳过同层重复”的回溯 (C++ 版本 `dfs_find_unique_combinations`)**

这是最常见、最简洁且广泛推荐的解决 `combinationSum2` 的方法。

1.  **预处理**：
    *   **排序 `candidates` 数组**：这是实现去重剪枝的**关键前提**。`std::sort(candidates.begin(), candidates.end());`

2.  **函数签名**：`dfs(candidates, start_idx, remaining_target, current_path, result_combinations)`
    *   `start_idx`：指示当前迭代应该从 `candidates` 数组的哪个索引开始选择数字。
    *   `current_path`：当前正在构建的组合。

3.  **核心逻辑**：
    *   **终止条件**：`remaining_target == 0` (找到解) 或 `remaining_target < 0` (剪枝)。
    *   **递归探索**：
        *   使用一个 `for` 循环，从 `start_idx` 开始遍历 `candidates` 数组。
        *   **核心去重剪枝**：在 `for` 循环内部，添加一个条件 `if (i > start_idx && candidates[i] == candidates[i - 1]) { continue; }`
            *   **目的**：确保在**同一层递归**中，对于**重复的数字**（例如 `[1, 1, 2]` 中的两个 `1`），我们只处理**第一个**，跳过后续的重复项。这避免了生成相同的组合，例如 `[1(idx=0), 2]` 和 `[1(idx=1), 2]`。
            *   `i > start_idx` 是关键，它确保只有当 `candidates[i]` 是**本层循环中 `candidates[i-1]` 的同级重复项**时才跳过。这允许 `[1,1,1]` 这种组合，因为 `[1,1]` 之后，`[1,1,1]` 是在**不同递归层级**通过选择 `candidates[i]` 得到的。
        *   **选择**：将 `candidates[i]` 添加到 `current_path`。
        *   **递归**：调用 `dfs`，`start_idx` 更新为 `i + 1`（因为**每个数字在组合中只能使用一次**）。`remaining_target` 减去 `candidates[i]`。
        *   **回溯**：递归返回后，将 `candidates[i]` 从 `current_path` 中移除。

4.  **特点**：
    *   **简洁优雅**：去重逻辑直接内嵌在 `for` 循环中，非常高效。
    *   **通用性强**：这种去重模式适用于许多“组合问题”的变体。
    *   `start_idx` 的正确传递（`i + 1`）确保了元素在单个组合内不重复使用。

---

#### **思路二：基于“预处理频率 + 选择次数”的回溯 (C 版本 `dfs`)**

这种方法将候选数字预处理成` (value, count)` 的频率对，然后在回溯时决策每个数字要使用多少次。

1.  **预处理**：
    *   **排序 `candidates` 数组**：`qsort(candidates, ...)`。
    *   **统计频率**：遍历排序后的 `candidates` 数组，将其转换为一个 `(value, count)` 对的列表（例如存储在全局 `freq` 数组中）。
        *   `freq[pos][0]` 存储数字值，`freq[pos][1]` 存储该数字在 `candidates` 中出现的总次数。
        *   这一步将 `candidates = [10, 1, 2, 7, 6, 1, 5]` 转换为 `freq = [[1, 2], [2, 1], [5, 1], [6, 1], [7, 1], [10, 1]]`。

2.  **函数签名**：`dfs(pos, rest)`
    *   `pos`：当前正在考虑的 `freq` 数组中的元素索引。
    *   `rest`：剩余需要凑成的目标值。
    *   `sequence`：当前正在构建的组合。

3.  **核心逻辑**：
    *   **终止条件**：`rest == 0` (找到解) 或 `pos == freqSize` (遍历完所有不同的数字) 或 `rest < freq[pos][0]` (当前数字太大无法凑出 `rest`)。
    *   **递归探索**：
        *   **策略一：跳过当前数字 `freq[pos][0]`**
            *   直接递归调用 `dfs(pos + 1, rest)`，去考虑下一个**不同的**数字，而不使用 `freq[pos][0]`。
        *   **策略二：选择当前数字 `freq[pos][0]` 若干次**
            *   计算 `most`：当前数字 `freq[pos][0]` 最多可以使用的次数。`most = min(rest / freq[pos][0], freq[pos][1])`。
            *   使用一个 `for` 循环，从 `i = 1` 迭代到 `most`。在每次迭代中，我们尝试选择 `freq[pos][0]` 这个数字 `i` 次。
            *   将 `i` 个 `freq[pos][0]` 添加到 `sequence` 路径中。
            *   **递归调用** `dfs(pos + 1, rest - i * freq[pos][0])`，去考虑下一个**不同的**数字。
            *   **回溯**：递归返回后，将 `i` 个 `freq[pos][0]` 从 `sequence` 中移除。

4.  **特点**：
    *   **去重隐式**：通过预处理 `freq` 数组，所有重复的数字在 `dfs` 中被统一处理。`dfs` 的 `pos` 始终指向**不同**的数字。
    *   **效率可能更高**：对于候选集中有大量重复数字的情况，这种方法可以减少 `for` 循环的迭代次数，因为它直接决策“用多少个”。
    *   **复杂性增加**：需要额外的预处理步骤和 `freq` 数组来存储频率。

---

**总结差异与建议：**

*   **C++ 版 (排序 + 遍历跳过同层重复)**：
    *   **优点**：代码更简洁，更容易理解和实现，没有额外的预处理步骤。去重逻辑直接内嵌在主循环中，非常优雅。
    *   **缺点**：对于每个 `candidates[i]`，即使是重复的，也会在 `for` 循环中被访问，通过 `continue` 跳过。
    *   **推荐程度**：**高**，这是 LeetCode 竞赛和面试中最常用的方法。

*   **C 版 (预处理频率 + 选择次数)**：
    *   **优点**：对于候选集中有**大量重复数字**且 `candidatesSize` 很大的情况，它可以显著减少 `dfs` 中 `for` 循环的迭代次数，从而提高性能。
    *   **缺点**：增加了预处理阶段的复杂性，需要额外的 `freq` 数组。代码实现相对复杂一些，特别是手动管理 `i` 个元素的 `push_back` 和 `pop_back`。
    *   **推荐程度**：在某些极端测试用例下，如果存在大量重复，此方法可能略快，但总体上，简洁性不如 C++ 版。

对于 `combinationSum2`，我个人会优先推荐**C++ 版本的“排序 + 遍历跳过同层重复”的思路**，因为它在代码实现和理解上更直接，且效率通常也足够高。C 版本的“预处理频率”是一种更高级的优化，但在许多情况下，简洁性会牺牲一点。

### Way Of C

> source code

```c
/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
int** ans;
int* ansColumnSizes;
int ansSize;

int* sequence;
int sequenceSize;

int** freq;
int freqSize;

void dfs(int pos, int rest) {
    if (rest == 0) {
        int* tmp = malloc(sizeof(int) * sequenceSize);
        memcpy(tmp, sequence, sizeof(int) * sequenceSize);
        ans[ansSize] = tmp;
        ansColumnSizes[ansSize++] = sequenceSize;
        return;
    }
    if (pos == freqSize || rest < freq[pos][0]) {
        return;
    }

    dfs(pos + 1, rest);

    int most = fmin(rest / freq[pos][0], freq[pos][1]);
    for (int i = 1; i <= most; ++i) {
        sequence[sequenceSize++] = freq[pos][0];
        dfs(pos + 1, rest - i * freq[pos][0]);
    }
    sequenceSize -= most;
}

int comp(const void* a, const void* b) {
    return *(int*)a - *(int*)b;
}

int** combinationSum2(int* candidates, int candidatesSize, int target, int* returnSize, int** returnColumnSizes) {
    ans = malloc(sizeof(int*) * 2001);
    ansColumnSizes = malloc(sizeof(int) * 2001);
    sequence = malloc(sizeof(int) * 2001);
    freq = malloc(sizeof(int*) * 2001);
    ansSize = sequenceSize = freqSize = 0;

    qsort(candidates, candidatesSize, sizeof(int), comp);
    for (int i = 0; i < candidatesSize; ++i) {
        if (freqSize == 0 || candidates[i] != freq[freqSize - 1][0]) {
            freq[freqSize] = malloc(sizeof(int) * 2);
            freq[freqSize][0] = candidates[i];
            freq[freqSize++][1] = 1;
        } else {
            ++freq[freqSize - 1][1];
        }
    }
    dfs(0, target);
    *returnSize = ansSize;
    *returnColumnSizes = ansColumnSizes;
    return ans;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    std::vector<std::vector<int>> result_combinations; // 存储所有找到的组合
    std::vector<int> current_path;                     // 存储当前组合路径

    std::vector<std::vector<int>> combinationSum2(std::vector<int>& candidates, int target) {
        // 清空之前调用的状态（如果 Solution 对象被重复使用）
        result_combinations.clear();
        current_path.clear();

        // ！！！非常关键：先对候选数字进行排序，这是实现去重剪枝的前提
        std::sort(candidates.begin(), candidates.end());
        
        // 调用深度优先搜索函数，从第一个候选数字开始，目标值为 target
        dfs_find_unique_combinations(candidates, 0, target);
        return result_combinations;
    }

private:
    // 深度优先搜索（回溯）函数
    // candidates: 候选数字数组 (已排序)
    // start_idx: 当前搜索的起始索引
    // remaining_target: 剩余需要凑成的目标值
    void dfs_find_unique_combinations(
        std::vector<int>& candidates,
        int start_idx,
        int remaining_target
    ) {
        // 剪枝条件：如果剩余目标值小于 0，说明当前路径的和已经超过 target，无效
        if (remaining_target < 0) {
            return;
        } 
        // 终止条件：如果剩余目标值等于 0，说明找到了一个有效组合
        else if (remaining_target == 0) {
            result_combinations.push_back(current_path); // 将当前组合路径加入结果集
            return; // 找到一个解，返回
        } 
        // 递归探索：如果剩余目标值大于 0，继续搜索
        else {
            // 从 start_idx 开始遍历候选数字。
            // 确保在同一层递归中，对于重复的数字，只选择一次。
            for (int i = start_idx; i < candidates.size(); ++i) {
                // ！！！核心去重逻辑：
                // 如果当前数字 candidates[i] 与前一个数字 candidates[i-1] 相同，
                // 并且前一个数字 (candidates[i-1]) 已经被处理过了 (i > start_idx)，
                // 则跳过当前数字，以避免生成重复的组合。
                // 例如 candidates = [1,1,2]，target = 3。
                // 第一次循环 i=0 (选择第一个1)。
                // 第二次循环 i=1 (选择第二个1)。如果跳过，[1,1,2]只生成一次。
                if (i > start_idx && candidates[i] == candidates[i - 1]) {
                    continue; // 跳过这个重复的数字
                }

                // 选择：将当前数字加入组合路径
                current_path.push_back(candidates[i]); 
                
                // 递归：尝试找到剩余的目标值
                // ！！！关键点：由于每个数字在组合中只能使用一次，下一轮搜索从 i + 1 开始。
                dfs_find_unique_combinations(candidates, i + 1, remaining_target - candidates[i]);
                
                // 回溯：移除当前数字，尝试下一个不同的数字
                current_path.pop_back(); 
            }
        }
    }
};
```
