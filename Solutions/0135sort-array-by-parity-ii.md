https://leetcode.cn/problems/sort-array-by-parity-ii

### Think
```
Two points
```

### Ways
By C++
```C++
class Solution {
public:
    vector<int> sortArrayByParityII(vector<int>& nums) {
        int n = nums.size();
        int j = 1;
        for (int i = 0; i < n; i += 2) {
            if (nums[i] % 2 == 1) {
                while (nums[j] % 2 == 1) j += 2;
                swap(nums[i], nums[j]);
            }
        }
        return nums;
    }
};
```
