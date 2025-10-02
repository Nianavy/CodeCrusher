# Question

[combination-sum](https://leetcode.cn/problems/combination-sum/)



# Answer

> solution

解决这个问题的两种基本思路都是**回溯（Backtracking）**。它们的核心都是通过深度优先搜索来探索所有可能的组合路径。主要区别在于如何组织搜索空间和处理“元素可重复使用”这个条件。一种是在循环中选择，另一种是针对当前元素决定“用”还是“不用”。

### **`combinationSum` 问题的两种解决思路总结**

`combinationSum` 问题要求从给定的候选数字集中找出所有和为目标值的组合，其中同一个数字可以重复使用。

---

#### **思路一：基于“循环选择”的回溯（C++ 版本 `dfs_find_combinations`）**

这是最常见且直观的回溯实现方式，也是你 C++ 代码所采用的。

1.  **函数签名**：`dfs(candidates, start_idx, remaining_target, current_combination, all_combinations)`
    *   `start_idx`：非常关键，它指示当前迭代应该从 `candidates` 数组的哪个索引开始选择数字。

2.  **核心逻辑**：
    *   **终止条件**：
        *   `remaining_target == 0`：找到一个有效组合，将其添加到结果集并返回。
        *   `remaining_target < 0`：当前路径无效，剪枝并返回。
    *   **递归探索**：
        *   使用一个 `for` 循环，从 `start_idx` 开始遍历 `candidates` 数组。
        *   在循环的每次迭代中，我们**选择** `candidates[i]`：
            *   将 `candidates[i]` 添加到 `current_combination` 路径中。
            *   **递归调用** `dfs`：由于元素可以重复使用，下一层递归的 `start_idx` 仍然是当前的 `i`（允许在下一次递归中再次选择 `candidates[i]`）。`remaining_target` 减去 `candidates[i]`。
            *   **回溯**：递归返回后，将 `candidates[i]` 从 `current_combination` 中移除（撤销选择），以便在当前循环中尝试 `candidates[i+1]`。

3.  **特点**：
    *   通过 `start_idx` 控制了组合的顺序，自动避免了重复组合（例如 `[2,3]` 和 `[3,2]` 只会生成一次）。
    *   `for` 循环自然地处理了“选择”和“回溯”的迭代。
    *   **重复使用元素**的逻辑由递归时传递**相同的 `start_idx`** (即 `i`) 来实现。

---

#### **思路二：基于“跳过/选择多次”的回溯（C 版本 `dfs`）**

这种方法也有效，但组织方式略有不同。它不是在循环中逐个选择，而是针对当前的 `candidates[idx]`，决定“不选它”或“选它并继续选它”。

1.  **函数签名**：`dfs(candidates, target, ans, combine, idx)`
    *   `idx`：当前正在考虑的 `candidates` 数组中的元素索引。

2.  **核心逻辑**：
    *   **终止条件**：
        *   `target == 0`：找到一个有效组合，将其添加到结果集并返回。
        *   `idx == candidatesSize_tmp`（即 `idx` 遍历完所有候选数字）：如果此时 `target` 仍不为 `0`，说明当前路径无效，返回。
    *   **递归探索**：
        *   **策略一：跳过 `candidates[idx]`**
            *   直接递归调用 `dfs(candidates, target, ans, combine, idx + 1)`，去考虑下一个候选数字 `candidates[idx+1]`，而不使用 `candidates[idx]`。
        *   **策略二：选择 `candidates[idx]`**
            *   如果 `target - candidates[idx] >= 0`（即选择当前数字后，目标值仍然非负）：
                *   将 `candidates[idx]` 添加到 `combine` 路径中。
                *   **递归调用** `dfs(candidates, target - candidates[idx], ans, combine, idx)`。这里的 `idx` **保持不变**，允许在下一层递归中**再次选择** `candidates[idx]`。
                *   **回溯**：递归返回后，将 `candidates[idx]` 从 `combine` 中移除。

3.  **特点**：
    *   通过递归分支来处理“选”与“不选”，而不是通过 `for` 循环。
    *   **重复使用元素**的逻辑由递归时传递**相同的 `idx`** 来实现。
    *   代码结构可能看起来像是一个二叉树的遍历，每个节点都有“左分支（不选）”和“右分支（选）”。

---

**总结差异与建议：**

虽然两种思路都能正确解决问题，但**“循环选择”的回溯（C++ 版本的 `dfs_find_combinations`）通常被认为更简洁、更易理解和实现**。它自然地整合了选择和回溯的步骤在一个 `for` 循环中，而且 `start_idx` 的管理更清晰地防止了重复组合。

“跳过/选择多次”的回溯在某些问题中（特别是当每个元素只能选择一次，且有重复元素需要去重时）有其优势，但对于 `combinationSum` 这种元素可重复选择的问题，其代码逻辑有时不如“循环选择”直观。

### Way Of C

> source code

```c
int candidatesSize_tmp, ansSize, combineSize, *ansColumnSize;

void dfs(int* candidates, int target, int** ans, int* combine, int idx) {
    if (idx == candidatesSize_tmp) {
        return;
    }
    if (target == 0) {
        int* tmp = malloc(sizeof(int) * combineSize);
        for (int i = 0; i < combineSize; ++i) {
            tmp[i] = combine[i];
        }
        ans[ansSize] = tmp;
        ansColumnSize[ansSize++] = combineSize;
        return;
    }
    // 直接跳过
    dfs(candidates, target, ans, combine, idx + 1);
    // 选择当前数
    if (target - candidates[idx] >= 0) {
        combine[combineSize++] = candidates[idx];
        dfs(candidates, target - candidates[idx], ans, combine, idx);
        combineSize--;
    }
}

int** combinationSum(int* candidates, int candidatesSize, int target, int* returnSize, int** returnColumnSizes) {
    candidatesSize_tmp = candidatesSize;
    ansSize = combineSize = 0;
    int** ans = malloc(sizeof(int*) * 1001);
    ansColumnSize = malloc(sizeof(int) * 1001);
    int combine[2001];
    dfs(candidates, target, ans, combine, 0);
    *returnSize = ansSize;
    *returnColumnSizes = ansColumnSize;
    return ans;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    // 主函数：计算所有满足条件的组合
    std::vector<std::vector<int>> combinationSum(std::vector<int>& candidates, int target) {
        std::vector<std::vector<int>> result; // 存储所有找到的组合
        std::vector<int> current_combination; // 存储当前正在构建的组合路径

        // 理论上，如果 candidates 有序，可以加速剪枝。
        // 题目未强制要求有序，但通常排序是一个好的优化。
        // std::sort(candidates.begin(), candidates.end());

        // 调用深度优先搜索函数，从第一个候选数字开始，目标值为 target
        dfs_find_combinations(candidates, 0, target, current_combination, result);
        return result;
    }

private:
    // 深度优先搜索（回溯）函数
    // candidates: 候选数字数组
    // start_idx: 当前搜索的起始索引，确保不重复选择已考虑的数字（用于去重，此问题中元素可重复，所以传递的是 i）
    // remaining_target: 剩余需要凑成的目标值
    // current_combination: 当前组合路径 (按引用传递，以便修改)
    // all_combinations: 存储所有找到的组合 (按引用传递，以便添加结果)
    void dfs_find_combinations(
        std::vector<int>& candidates,
        int start_idx,
        int remaining_target,
        std::vector<int>& current_combination,
        std::vector<std::vector<int>>& all_combinations
    ) {
        // 剪枝条件：如果剩余目标值小于 0，说明当前路径的和已经超过 target，无效
        if (remaining_target < 0) {
            return;
        } 
        // 终止条件：如果剩余目标值等于 0，说明找到了一个有效组合
        else if (remaining_target == 0) {
            all_combinations.push_back(current_combination); // 将当前组合路径加入结果集
            return; // 找到一个解，返回
        } 
        // 递归探索：如果剩余目标值大于 0，继续搜索
        else {
            // 从 start_idx 开始遍历候选数字。
            // 关键点：由于元素可以重复使用，下一轮递归的起始索引仍然是当前的 i。
            // 这样允许在同一层递归中重复选择 candidates[i]。
            for (int i = start_idx; i < candidates.size(); ++i) {
                // 选择：将当前数字加入组合路径
                current_combination.push_back(candidates[i]); 
                
                // 递归：尝试找到剩余的目标值
                // 注意：这里传递的 start_idx 仍然是 i，允许重复选择 candidates[i]
                dfs_find_combinations(candidates, i, remaining_target - candidates[i], current_combination, all_combinations);
                
                // 回溯：移除当前数字，尝试下一个不同的数字
                current_combination.pop_back(); 
            }
        }
    }
};
```
