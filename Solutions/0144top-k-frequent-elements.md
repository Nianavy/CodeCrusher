https://leetcode.cn/problems/top-k-frequent-elements

### Think
```
Heap
```

### Ways
By C++
```C++
class Solution {
public:
    static bool cmp(pair<int, int> &m, pair<int, int> &n) {
        return m.second > n.second;
    }
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> occurrences;
        for (auto &v : nums) occurrences[v]++;

        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(&cmp)> q(cmp);
        for (auto &[num, count] : occurrences) {
            if (q.size() == k) {
                if (q.top().second < count) {
                    q.pop();
                    q.emplace(num, count);
                }
            }
            else q.emplace(num, count);
        }
        vector<int> ret;
        while (!q.empty()) {
            ret.emplace_back(q.top().first);
            q.pop();
        } 
        return ret;
    }
};
```
