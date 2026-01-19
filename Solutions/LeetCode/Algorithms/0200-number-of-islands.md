# Question

[number-of-islands](https://leetcode.cn/problems/number-of-islands/)



# Answer

> solution

**解决思路（BFS/DFS）**
1. **遍历整个网格**，扫描每一个格子。
2. **当发现一个陆地格子（'1'）**，说明发现了新岛屿：
   - 岛屿数 +1。
   - **立即将这个岛屿所有陆地格子“沉没”成水（'0'）**，避免重复计数。
3. **沉没过程**：从该格子开始，使用BFS或DFS遍历其**上下左右**所有相连的陆地，全部改为 '0'。
4. **继续扫描**，直到整个网格遍历完。

**关键点**
- **沉没策略**：沉没被访问过的陆地，是避免重复计数和无限循环的最简方法。
- **遍历方向**：只检查上下左右四个方向。
- **数据结构**：维度由输入决定，核心是对网格的修改（沉没）。

**复杂度**
- **时间**：`O(M*N)`，每个格子只访问一次。
- **空间**：`O(M*N)`，BFS队列或递归栈（最坏情况）。

**本质**
> **“看见陆地就计数，然后把这片陆地整个沉掉，让下一个陆地成为新岛屿。”**

### Way Of C

> source code

```c
int numIslands(char** grid, int gridSize, int* gridColSize) {
    if (grid == NULL || gridSize <= 0 || gridColSize == NULL) {
        return 0;
    }
    
    // 关键：使用传入的 gridColSize 指针获取列数
    // 它应该已经指向了正确的列数（调用者已设置）
    int colSize = *gridColSize;
    
    // 检查列数是否合法
    if (colSize <= 0) {
        return 0;
    }
    
    // 如果 grid 是 NULL 或空行，处理安全情况
    if (grid[0] == NULL) {
        return 0;
    }
    
    // 现在我们有了正确的列数，不需要 strlen！
    // 每一行都应该有 colSize 个字符，可能没有终止符。
    // 这是C的危险区域，但我们在控制它们的使用。
    
    int islands = 0;
    const int dx[] = {-1, 0, 1, 0};
    const int dy[] = {0, 1, 0, -1};
    const int dirs = 4;
    
    // 动态分配队列
    int maxQueueSize = gridSize * colSize;
    int* q_row = malloc(maxQueueSize * sizeof(int));
    int* q_col = malloc(maxQueueSize * sizeof(int));
    if (q_row == NULL || q_col == NULL) {
        free(q_row); free(q_col);
        return -1;
    }
    
    // 核心算法：使用 colSize 作为边界
    for (int i = 0; i < gridSize; i++) {
        if (grid[i] == NULL) continue; // 安全检查
        
        for (int j = 0; j < colSize; j++) {
            if (grid[i][j] == '1') {
                islands++;
                
                // BFS
                int head = 0, tail = 0;
                grid[i][j] = '0';
                q_row[tail] = i;
                q_col[tail] = j;
                tail++;
                
                while (head < tail) {
                    int x = q_row[head];
                    int y = q_col[head];
                    head++;
                    
                    for (int k = 0; k < dirs; k++) {
                        int nx = x + dx[k];
                        int ny = y + dy[k];
                        
                        // 边界检查：使用 colSize 而不是 strlen
                        if (nx >= 0 && nx < gridSize && ny >= 0 && ny < colSize && grid[nx][ny] == '1') {
                            grid[nx][ny] = '0';
                            q_row[tail] = nx;
                            q_col[tail] = ny;
                            tail++;
                        }
                    }
                }
            }
        }
    }
    
    free(q_row);
    free(q_col);
    return islands;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        if (grid.empty() || grid[0].empty()) return 0;
        
        int rows = grid.size();
        int cols = grid[0].size();
        int islands = 0;
        
        const int dx[4] = {-1, 0, 1, 0};
        const int dy[4] = {0, 1, 0, -1};
        
        queue<pair<int, int>> q;
        
        for (int i = 0; i < rows; ++i) {
            for (int j = 0; j < cols; ++j) {
                if (grid[i][j] == '1') {
                    islands++;
                    // BFS to sink the island.
                    grid[i][j] = '0';
                    q.push({i, j});
                    
                    while (!q.empty()) {
                        auto [x, y] = q.front();
                        q.pop();
                        
                        for (int k = 0; k < 4; ++k) {
                            int nx = x + dx[k];
                            int ny = y + dy[k];
                            
                            if (nx >= 0 && nx < rows &&
                                ny >= 0 && ny < cols &&
                                grid[nx][ny] == '1') {
                                grid[nx][ny] = '0';
                                q.push({nx, ny});
                            }
                        }
                    }
                }
            }
        }
        
        return islands;
    }
};
```
