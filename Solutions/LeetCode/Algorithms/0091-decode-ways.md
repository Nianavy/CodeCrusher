# Question

[decode-ways](https://leetcode.cn/problems/decode-ways/)



# Answer

> solution

### O(1) 空间动态规划

解决这个问题的思路，必须围绕**“如何在 O(1) 空间内高效地维护 DP 状态”**：

1.  **第一步永远是简化数据结构（状态定义）。**
    *   定义三个变量：
        *   `ways_two_back` (W2)：表示 `f[i-2]`，即到前两位字符为止的解码方法数。
        *   `ways_one_back` (W1)：表示 `f[i-1]`，即到前一位字符为止的解码方法数。
        *   `current_ways` (WC)：表示 `f[i]`，即到当前字符为止的解码方法数。

2.  **建立转移逻辑（消除特殊情况）。**
    当前的解码方法数 `WC` 是由两部分组成：
    *   **单字符解码**：如果当前字符 `s[i]` 不是 `'0'`，则 `WC` 至少等于 `W1`。
    *   **双字符解码**：如果前一个字符 `s[i-1]` 和当前字符 `s[i]` 组成的数字在 `10` 到 `26` 之间，则 `WC` 加上 `W2`。

3.  **处理边界和零。**
    *   如果输入为空或以 `'0'` 开头，方法数为 0。
    *   在每次状态转移后，如果 `WC` 为 0，说明遇到了不可解码的序列（例如 `...30`, `...00`），此时整个字符串的解码数就是 0，直接返回。

4.  **状态更新。**
    在循环的每一步结束时，更新状态，为下一次迭代做准备：`W2 = W1`，`W1 = WC`。

这就是核心思路：用最少的资源（O(1) 空间）和最直接的逻辑，实现 O(N) 时间的动态规划求解。

### Way Of C

> source code

```c
// O(1) space DP. Clear state variables are mandatory.
int numDecodings(char* s) {
    if (!s || *s == '\0')
        return 0;

    // ways_two_back: f[i-2]
    int ways_two_back = 1; 

    // ways_one_back: f[i-1]. If the first char is '0', it's zero.
    int ways_one_back = (*s == '0') ? 0 : 1;
    
    // Iterate from the second character onwards.
    for (char *p = s + 1; *p != '\0'; p++) {
        int current_ways = 0;
        
        // 1. Single digit decoding (if current char is not '0')
        if (*p >= '1' && *p <= '9') {
            current_ways = ways_one_back;
        }

        // 2. Double digit decoding (check previous char)
        char prev_char = *(p - 1);
        int num = (prev_char - '0') * 10 + (*p - '0');
        
        if (num >= 10 && num <= 26) {
            current_ways += ways_two_back;
        }
        
        // State update
        ways_two_back = ways_one_back;
        ways_one_back = current_ways;

        // Early exit for un-decodable strings (e.g., "00", "30")
        if (ways_one_back == 0 && *p == '0' && prev_char != '1' && prev_char != '2')
            return 0;
    }

    return ways_one_back;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int numDecodings(const std::string& s) {
        if (s.empty() || s[0] == '0') {
            return 0;
        }

        // f[i-2]
        int ways_two_back = 1; 
        // f[i-1] (since s[0] is guaranteed not '0', f[1] is 1)
        int ways_one_back = 1; 
        
        for (int i = 1; i < s.length(); ++i) {
            int current_ways = 0;
            char current_char = s[i];
            char prev_char = s[i - 1];

            // Single digit decode
            if (current_char != '0') {
                current_ways = ways_one_back;
            }

            // Double digit decode
            int num = (prev_char - '0') * 10 + (current_char - '0');
            if (num >= 10 && num <= 26) {
                current_ways += ways_two_back;
            }
            
            // Critical check: if current_ways is 0, the string is undecodable 
            // from this point on (e.g., "30", "00", "06").
            if (current_ways == 0) {
                return 0;
            }

            // State shift
            ways_two_back = ways_one_back;
            ways_one_back = current_ways;
        }

        return ways_one_back;
    }
};
```
