https://leetcode.cn/problems/sum-of-all-odd-length-subarrays

### think:

遍历数组 *arr* 中的每个长度为奇数的子数组，计算这些子数组的和。

### ways:

By C++:

Brute force:

```cpp
class Solution {
public:
    int sumOddLengthSubarrays(vector<int>& arr) {
        int sum = 0;
        int n = arr.size();
        for (int begin=0; begin<n; ++begin) {
            for (int length=1; begin+length<=n; length+=2) {
                int end = begin + length - 1;
                for (int i=begin; i<=end; ++i)
                    sum += arr[i];
            }
        }
        return sum;
    }
};
```

