https://leetcode.cn/problems/third-maximum-number

### Think
```
For-Each
```

### Ways
By C++
```C++
class Solution {
public:
    int thirdMax(vector<int>& nums) {
        long a = LONG_MIN, b = LONG_MIN, c = LONG_MIN;
        for (long num : nums) {
            if (num > a) {
                c = b;
                b = a;
                a = num;
            } else if (a > num && num > b) {
                c = b;
                b = num;
            } else if (b > num && num > c) c = num;
        }
        return c == LONG_MIN ? a : c;
    }
};
```
