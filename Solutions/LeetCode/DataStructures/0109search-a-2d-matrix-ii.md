https://leetcode.cn/problems/search-a-2d-matrix-ii

### Think
```
Binary Search
```

### Ways
By C++
```C++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        for (const auto& row: matrix) {
            auto it = lower_bound(row.begin(), row.end(), target);
            if (it != row.end() && *it == target) return true;
        }
        return false;
    }
};
```
