# Question

[surrounded-regions](https://leetcode.cn/problems/surrounded-regions/)



# Answer

> solution

【问题本质】
被 'X' 包围的 'O' 要变成 'X'。反过来想：从边界出发能到达的 'O' 才是安全的。

【解决思路】

第一层：算法选择
反向思维 + DFS。不是找被包围的 'O'，而是找从边界能到达的 'O'。

第二层：核心逻辑
1. 从所有边界的 'O' 开始 DFS
2. 标记所有能到达的 'O' 为临时标记（如 '#'）
3. 最后遍历整个棋盘：
   - '#' 变回 'O'（边界能到达，安全）
   - 其他 'O' 变成 'X'（被包围）

第三层：为什么这样做
直接找被包围的 'O' 很复杂（需要判断是否被完全包围）
反向找边界能到达的 'O' 简单直接
只需遍历边界 + 一次 DFS + 一次全盘扫描

第四层：实现细节
边界有4条边，但只需遍历一次（避免重复）
用临时标记代替修改原值，最后统一转换
时间 O(n×m)，空间 O(h)（递归栈深度）

### Way Of C

> source code

```c
void solve(char** board, int boardSize, int* boardColSize) {
    int n = boardSize, m = boardColSize[0];
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
    
    void dfs(int x, int y) {
        board[x][y] = '#';
        for (int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if (a >= 0 && a < n && b >= 0 && b < m && board[a][b] == 'O')
                dfs(a, b);
        }
    }
    
    for (int i = 0; i < n; i++) {
        if (board[i][0] == 'O') dfs(i, 0);
        if (board[i][m-1] == 'O') dfs(i, m-1);
    }
    for (int i = 0; i < m; i++) {
        if (board[0][i] == 'O') dfs(0, i);
        if (board[n-1][i] == 'O') dfs(n-1, i);
    }
    
    for (int i = 0; i < n; i++)
        for (int j = 0; j < m; j++)
            board[i][j] = board[i][j] == '#' ? 'O' : 'X';
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    void solve(vector<vector<char>>& board) {
        int n = board.size(), m = board[0].size();
        int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
        
        function<void(int, int)> dfs = [&](int x, int y) {
            board[x][y] = '#';
            for (int i = 0; i < 4; i++) {
                int a = x + dx[i], b = y + dy[i];
                if (a >= 0 && a < n && b >= 0 && b < m && board[a][b] == 'O')
                    dfs(a, b);
            }
        };
        
        for (int i = 0; i < n; i++) {
            if (board[i][0] == 'O') dfs(i, 0);
            if (board[i][m-1] == 'O') dfs(i, m-1);
        }
        for (int i = 0; i < m; i++) {
            if (board[0][i] == 'O') dfs(0, i);
            if (board[n-1][i] == 'O') dfs(n-1, i);
        }
        
        for (int i = 0; i < n; i++)
            for (int j = 0; j < m; j++)
                board[i][j] = board[i][j] == '#' ? 'O' : 'X';
    }
};
```
