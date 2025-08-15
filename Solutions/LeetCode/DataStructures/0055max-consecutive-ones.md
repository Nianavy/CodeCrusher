https://leetcode.cn/problems/max-consecutive-ones

### Think
```TXT
Code is all.
```

### Ways
By C++:
```C++
class Solution {
public:
    int findMaxConsecutiveOnes(vector<int>& nums) {
        int ans = 0, cnt = 0;
        for (int x : nums) {
            if (x) {
                cnt++;
                ans = max(ans, cnt);
            } else {
                cnt = 0;
            }
        }
        return ans;
    }
};
```
