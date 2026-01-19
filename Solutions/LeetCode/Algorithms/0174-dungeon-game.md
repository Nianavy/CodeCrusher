# Question

[dungeon-game](https://leetcode.cn/problems/dungeon-game/)



# Answer

> solution

**解决思路总结**

1.  **问题本质**：从终点回溯，每个格子需要的最低生命值 = `min(从右边来的最低生命, 从下边来的最低生命) - 当前格子值`，且结果不能小于1。

2.  **算法选择**：
    - **最佳实践**：O(n*m) 时间，O(1) 或 O(n) 或 O(n*m) 空间的动态规划。根据场景选择：内存紧张用 O(n) 优化，代码清晰用 O(n*m) 显式矩阵。

3.  **关键实现点**：
    - **消除特殊情况**：不要为边界、内部分别写循环。用一个双重循环覆盖全部，用 `if` 检查越界，或扩展矩阵尺寸（多用一行一列）来统一处理。
    - **空间优化**（可选）：若用 O(n) 空间，注意状态依赖：计算 `dp[j]` 时，它既代表当前行，也代表下一行。需用临时变量保存旧值。
    - **边界防护**：空输入、矩阵为空、`malloc` 失败等需检查。

4.  **代码风格“好品味”**：
    - **简洁**：一个循环解决，避免多个冗余循环。
    - **稳健**：查空指针、防溢出、用无符号类型或大数（如 `1e8`）代表无穷。
    - **对齐**：C代码需管理内存，C++代码天然栈分配更安全。

5.  **最终选择**：优先**扩展矩阵法**（C++ 风格）或 **统一单循环法**（C 风格）。权衡清晰度与内存使用。

### Way Of C

> source code

```c
int calculateMinimumHP(int** dungeon, int dungeonSize, int* dungeonColSize) {
    // 1. 边界检查和输入有效性
    if (!dungeon || dungeonSize <= 0 || !dungeonColSize || dungeonColSize[0] <= 0) {
        return 1; // 返回一个安全的默认值
    }
    int n = dungeonSize;
    int m = dungeonColSize[0]; // 假设是矩形数组
    // 2. 分配 dp 矩阵。使用 calloc 初始化为 0。
    // 注意：在 LeetCode 上，如果 n 或 m 很大，这可能分配失败。
    // 我们会检查并优雅降级。
    int** dp = (int**)malloc(n * sizeof(int*));
    if (!dp) {
        return 1; // 内存不足，返回默认值
    }
    for (int i = 0; i < n; i++) {
        dp[i] = (int*)calloc(m, sizeof(int)); // 使用 calloc，避免后续判断时的未初始化值
        if (!dp[i]) {
            // 清理已分配的部分
            for (int k = 0; k < i; k++) free(dp[k]);
            free(dp);
            return 1;
        }
    }
    // 3. 从终点开始，统一处理所有情况（消除三个独立循环）
    // 初始化终点
    int hp_needed = 1 - dungeon[n-1][m-1];
    dp[n-1][m-1] = (hp_needed > 1) ? hp_needed : 1;
    // 倒序遍历所有格子
    for (int i = n - 1; i >= 0; i--) {
        for (int j = m - 1; j >= 0; j--) {
            if (i == n - 1 && j == m - 1) continue; // 终点已处理
            // 计算从右边和下边来的最小所需生命值
            int right = (j + 1 < m) ? dp[i][j + 1] : 1e8 + 7; // 如果右边无路，设为极大值
            int down = (i + 1 < n) ? dp[i + 1][j] : 1e8 + 7; // 如果下边无路，设为极大值
            
            // 选择最小的路径
            int min_next = (right < down) ? right : down;
            
            // 当前格子需要的生命值：从“下一点”需要的生命值 减去 当前格子的值。
            // 如果结果小于等于0，说明当前格子提供的生命值足以抵消“下一点”的需求，我们还需要1点初始生命值。
            // 如果结果大于0，那就是我们需要额外的生命值。
            // 公式可以统一为：max(1, min_next - dungeon[i][j])
            int need = min_next - dungeon[i][j];
            dp[i][j] = (need > 1) ? need : 1;
        }
    }
    // 4. 获取结果并 **释放内存** (切记！)
    int result = dp[0][0];
    
    for (int i = 0; i < n; i++) {
        free(dp[i]);
    }
    free(dp);
    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int calculateMinimumHP(vector<vector<int>>& dungeon) {
        if (dungeon.empty() || dungeon[0].empty()) return 1;
        
        int n = dungeon.size();
        int m = dungeon[0].size();
        
        // 使用一个额外的行和列来处理边界，这样可以消除循环内的 if 判断。
        // 这是“好品味”的体现：通过扩展数组来消除特殊情况。
        vector<vector<int>> dp(n + 1, vector<int>(m + 1, 1e8));
        
        // 设置终点的右和下邻居为1，这样 dp[n-1][m-1] 可以通过标准公式计算。
        dp[n][m-1] = dp[n-1][m] = 1;
        
        // 从终点开始，向后更新（实际上是从左下到右上，但dp依赖右下）
        for (int i = n - 1; i >= 0; i--) {
            for (int j = m - 1; j >= 0; j--) {
                // 统一计算，无需特殊判断
                int need = min(dp[i+1][j], dp[i][j+1]) - dungeon[i][j];
                dp[i][j] = (need > 1) ? need : 1;
            }
        }
        
        return dp[0][0];
    }
};
```
