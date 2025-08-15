https://leetcode.cn/problems/n-queens

### Think
```TXT
排列型回溯
```

### Ways
By C++
```C++
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> ans;
        vector<int> queens(n);
        vector<int> col(n), diag1(n*2-1), diag2(n*2-1);
        auto dfs = [&](this auto&& dfs, int r) {
            if (r==n) {
                vector<string> board(n);
                for (int i=0; i<n; ++i)
                    board[i] = string(queens[i], '.') + 'Q' + string(n-1-queens[i], '.');
                ans.push_back(board);
                return;
            }
            for (int c = 0; c < n; ++c) {
                int rc = r - c + n -1;
                if (!col[c] && !diag1[r+c] && !diag2[rc]) {
                    queens[r] = c;
                    col[c] = diag1[r+c] = diag2[rc] = true;
                    dfs(r+1);
                    col[c] = diag1[r+c] = diag2[rc] = false;
                }
            }
        };
        dfs(0);
        return ans;
    }
};
```
