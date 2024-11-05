https://leetcode.cn/problems/plus-one/

### think:

逆序遍历数组，找到第一个不为9的元素，加一，随后从该元素后一个元素开始循环置零。

数组遍历完成，对元素为9的数组要重新构造一个比原先多一个元素的数组（多的一个元素存放进位的1）。



### ways:

By C++:

```cpp
class Solution {
public:
    vector<int> plusOne(vector<int> &digits) {
        int n = digits.size();
        for (int i = n - 1; i >= 0; --i) {
            if (digits[i] != 9) {
                ++digits[i];
                for (int j = i + 1; j < n; ++j) {
                    digits[j] = 0;
                }
                return digits;
            }
        }

        vector<int> ans(n + 1);
        ans[0] = 1;
        return ans;      
    }
};
```

