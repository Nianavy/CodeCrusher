https://leetcode.cn/problems/k-closest-points-to-origin

### Think
```
just sort  
```

### Ways
By C++
```C++
class Solution {
public:
    vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
        sort(points.begin(), points.end(), [](const vector<int> &u, const vector<int> &v) {
            return u[0] * u[0] + u[1] * u[1] < v[0] * v[0] + v[1] * v[1];
        });
        return {points.begin(), points.begin() + k};
    }
};
```
