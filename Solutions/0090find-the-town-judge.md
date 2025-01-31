https://leetcode.cn/problems/find-the-town-judge

### Think
```
遍历每个节点的入度和出度，如果找到一个符合条件的节点，由于题目保证只有一个法官，我们可以直接返回结果；如果不存在符合条件的点，则返回 −1。
```

### Ways
By C++
```C++
class Solution {
public:
    int findJudge(int n, vector<vector<int>>& trust) {
        vector<int> inDegress(n + 1);
        vector<int> outDegress(n + 1);
        for (auto &edge : trust) {
            int x = edge[0], y = edge[1];
            ++inDegress[y];
            ++outDegress[x];
        }
        for (int i = 1; i <= n; ++i) {
            if (inDegress[i] == n - 1 && outDegress[i] == 0) return i;
        }
        return -1;
    }
};
```
