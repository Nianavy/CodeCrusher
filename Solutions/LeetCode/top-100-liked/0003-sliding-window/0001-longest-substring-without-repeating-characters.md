# Question

[longest-substring-without-repeating-characters](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)



# Answer

> solution

用数组 pos 存“字符上次出现的下一个位置”，
右指针不断延伸，左指针按需跳到重复字符的下一格，
滑动过程中实时更新最大无重复窗口长度。

### Way

> source code

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        if (s.empty()) return 0;

        size_t pos[256] = {0};          // 记录字符最后一次出现下标+1
        size_t left = 0, best = 0;

        for (size_t right = 0; right < s.size(); ++right) {
            unsigned char c = s[right];
            left = std::max(left, pos[c]);          // 重复则在重复字符下一字符处重新开始
            best = std::max(best, right - left + 1);
            pos[c] = right + 1;                     // 更新位置
        }
        return static_cast<int>(best);
    }
};
```
