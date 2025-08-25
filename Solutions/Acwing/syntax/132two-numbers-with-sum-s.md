https://www.acwing.com/problem/content/71/

```c++
class Solution {
public:
    vector<int> findNumbersWithSum(vector<int>& nums, int target) {
      vector<int> b;
      for (int i = 0; i < nums.size() - 1; ++i)
        for (int j = i + 1; j < nums.size(); ++j)
          if (nums[i] + nums[j] == target) {
            b.push_back(nums[i]);
            b.push_back(nums[j]);
            return b;
          }
    }
};
```
