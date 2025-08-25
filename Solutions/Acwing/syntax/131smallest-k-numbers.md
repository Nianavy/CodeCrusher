https://www.acwing.com/problem/content/49/

```c++
class Solution {
public:
    vector<int> getLeastNumbers_Solution(vector<int> input, int k) {
      priority_queue<int, vector<int>, greater<int>> heap;
      for (int x: input) heap.push(x);
      vector<int> res;
      while (k--) {
        res.push_back(heap.top());
        heap.pop();
      }
      return res;
    }
};
```
