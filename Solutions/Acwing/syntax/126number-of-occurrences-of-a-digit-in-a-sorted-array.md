https://www.acwing.com/problem/content/63/

```c++
class Solution {
private:
    int binarySearch(vector<int> &nums, int target, bool findFirst) {
      int left = 0, right = nums.size() - 1, result = -1;
      while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
          result = mid;
          if (findFirst) right = mid - 1;
          else left = mid + 1;
        }
        else if (nums[mid] < target) left = mid + 1;
        else right = mid - 1;
      }
      return result;
    }
    
public:
    int getNumberOfK(vector<int>& nums , int k) {
      int first = binarySearch(nums, k, true);
      int last = binarySearch(nums, k, false);
      if (first == -1 || last == -1) return 0;
      return last - first + 1;
    }
};
```
