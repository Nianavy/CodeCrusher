https://www.acwing.com/problem/content/64/

```c++
class Solution {
public:
    int getMissingNumber(vector<int>& nums) {
      int left = 0;
      int right = nums.size() - 1;
      while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == mid) left = mid + 1;
        else right = mid - 1;
      }
      return left;
    }
};
```
