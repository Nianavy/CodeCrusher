https://leetcode.cn/problems/add-binary

### think:

模拟

### ways:
By C++:

```cpp
class Solution {
public:
    string addBinary(string a, string b) {
        string ans;
        // 反转输入字符串以便从最低位开始相加
        reverse(a.begin(), a.end());
        reverse(b.begin(), b.end());

        int carry = 0;
        // 使用两个字符串的长度中较大的一个作为循环的边界
        for (size_t i = 0; i < max(a.size(), b.size()); ++i) {
            // 计算当前位的和以及进位
            carry += i < a.size() ? (a[i] == '1') : 0;
            carry += i < b.size() ? (b[i] == '1') : 0;
            // 将当前位的结果加到答案字符串的末尾，注意这里直接使用反转后的位置
            ans.push_back((carry & 1) ? '1' : '0');
            // 计算进位，使用右移操作代替除以2
            carry >>= 1;
        }

        // 如果最后还有进位，则添加到答案字符串
        if (carry) {
            ans.push_back('1');
        }

        // 反转答案字符串以得到正确的顺序
        reverse(ans.begin(), ans.end());

        return ans;
    }
};
```
