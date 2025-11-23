# Question

[subsets-ii](https://leetcode.cn/problems/subsets-ii/)



# Answer

> solution

### 核心思路：回溯算法 + 依赖排序的剪枝去重

#### 步骤一：预处理——创造消除重复的前提

**动作：** 必须对输入的数组 `nums` 进行排序。
**原因：** 这是所有去重逻辑的前提。排序后，所有重复的元素都紧密相邻。这使得我们可以通过局部检查（比较 `nums[i]` 和 `nums[i-1]`）来判断是否需要跳过当前元素。

#### 步骤二：通用回溯——遍历所有可能

**动作：** 定义一个深度优先搜索（DFS）函数，它维护一个当前路径（子集）和一个起始索引 `start`。
**逻辑：**
1.  **收集结果：** 每次进入 DFS，无论当前路径是否为空，都将其视为一个有效的子集并添加到结果集中。
2.  **递归推进：** 使用一个循环从 `start` 索引开始，遍历剩下的元素。
3.  **状态变化：**
    *   **选择：** 将 `nums[i]` 加入当前路径。
    *   **深入：** 递归调用 DFS，将起始索引设为 `i + 1` (确保不会重复选择同一个位置的元素)。
    *   **回溯：** 撤销选择（C++ 中是 `pop_back`；C 中是依赖 `level` 索引的自动覆盖）。

#### 步骤三：剪枝——关键去重逻辑

**目标：** 确保在 DFS 的**同一层级**，重复元素只被选择一次。

**判断条件：** `if (i > start && nums[i] == nums[i-1]) { continue; }`

**原理剖析：**
*   **`i > start`：** 确保我们只在循环的**第二次及以后**遇到重复元素时才跳过。当 `i == start` 时，我们总是需要处理这个元素（它是本层级的起点）。
*   **`nums[i] == nums[i-1]`：** 识别出当前元素与前一个元素相同。
*   **效果：** 通过 `i > start` 约束，我们保证了：
    *   如果我们在同一层级（同一个父节点），我们只允许**第一个**相同的元素被选入。
    *   如果我们在不同的层级（即 `nums[i-1]` 在上一层已经被选中并带入了新的递归），则不会触发剪枝，因为 `start` 已经被更新了。这正是我们想要的：允许 `[1, 1]` 这样的子集存在。

**总结：** 解决思路就是经典的 DFS 回溯，辅以**依赖预排序**的局部剪枝机制来排除重复的子集。

### Way Of C

> source code

```c
// Helper function for qsort
static int compare(const void *a, const void *b)
{
    return *(int *) a - *(int *) b;
}

// 预估容量：我们至少需要 2^N 个子集。
#define INITIAL_CAPACITY 2048 // 足够处理 N=10 的情况 (2^10 = 1024)

static void dfs_c(int *nums, int size, int start, int *buf,
                int level, int ***sets, int *count, int **sizes)
{
    // --- 1. 保存当前子集 ---
    // 假设 INITIAL_CAPACITY 足够大，否则需要在这里进行 realloc 检查。
    
    // 动态分配内存给当前子集
    (*sets)[*count] = (int*)malloc(level * sizeof(int));
    // 复制数据
    memcpy((*sets)[*count], buf, level * sizeof(int));
    // 保存子集大小
    (*sizes)[*count] = level;
    (*count)++;
    
    // --- 2. 遍历当前层级，选择下一个元素 ---
    for (int i = start; i < size; i++) {
        
        // 剪枝关键：避免在同一层级重复选择相同的元素。
        // 如果 i > start 且 nums[i] == nums[i-1]，说明 nums[i-1] 已经作为
        // 本层级的选择被处理过了，跳过 nums[i]。
        if (i > start && nums[i] == nums[i-1]) {
            continue; 
        }

        /* 选择 nums[i] */
        buf[level] = nums[i];
        
        /* 递归到下一层，下一层从 i + 1 开始选择 */
        dfs_c(nums, size, i + 1, buf, level + 1, sets, count, sizes);
        
        /* C 语言无需显式撤销：buf 通过 level 索引管理，回溯时 level 减小，覆盖旧数据 */
    }
}

/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
int** subsetsWithDup(int* nums, int numsSize, int* returnSize, int** returnColumnSizes) 
{
    // 1. 排序：这是剪枝的前提
    qsort(nums, numsSize, sizeof(int), compare);
    
    // 2. 初始化返回结构 (使用指针的指针来避免局部变量问题)
    int **sets = (int**)malloc(INITIAL_CAPACITY * sizeof(int *));
    int *buf = (int*)malloc(numsSize * sizeof(int)); // 用于存储当前路径
    *returnColumnSizes = (int*)malloc(INITIAL_CAPACITY * sizeof(int));
    *returnSize = 0;
    
    // 3. 执行回溯
    // 注意：sets, returnSize, returnColumnSizes 必须传址，以便 dfs_c 能修改它们的内容。
    dfs_c(nums, numsSize, 0, buf, 0, &sets, returnSize, returnColumnSizes);
    
    // 4. 清理路径缓冲区
    free(buf);
    
    // 5. 返回结果 (在实际生产环境中，这里应该 realloc 缩小 sets 和 *returnColumnSizes)
    return sets;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> res;
        // 1. 必须排序，确保重复元素相邻
        sort(nums.begin(), nums.end());
        
        // 使用成员变量 stack 简化参数传递
        path.clear(); 
        dfs(nums, 0, res);
        return res;    
    }

private:
    vector<int> path; // 用于构建当前子集的路径栈
    
    void dfs(vector<int>& nums, int start, vector<vector<int>>& res) {
        // 总是将当前路径（子集）加入结果，包括空集
        res.push_back(path);
        
        int n = nums.size();
        
        for (int i = start; i < n; i++) {
            // 剪枝关键点：如果当前元素 (nums[i]) 与前一个元素 (nums[i-1]) 相同，
            // 且前一个元素没有被包含（即 i > start），则跳过。
            // 解释：如果 i > start 且 nums[i] == nums[i-1]，
            // 意味着 nums[i-1] 已经作为本层级的“首次选择”被处理过了。
            // 如果我们从 start 开始选择，则不需要担心重复，因为 i 总是大于 i-1。
            
            // 更简洁的判断（基于排序和前一个元素是否被跳过）：
            if (i > start && nums[i] == nums[i-1]) {
                continue; 
            }
            
            // 1. 选择 nums[i]
            path.push_back(nums[i]);
            
            // 2. 递归到下一层，从 i + 1 开始选
            dfs(nums, i + 1, res);
            
            // 3. 回溯：撤销选择
            path.pop_back();
        }
    }
};
```
