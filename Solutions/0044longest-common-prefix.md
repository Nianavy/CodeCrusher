https://leetcode.cn/problems/longest-common-prefix

### Think
```TXT
思路分析

检查空数组：
首先，代码通过if (strs.empty()) return "";检查输入的字符串数组是否为空。如果为空，直接返回空字符串，因为没有公共前缀。

选择基准字符串：
代码将第一个字符串strs[0]赋值给s0，作为比较的基准。因为最长公共前缀不可能比第一个字符串还短。

逐字符比较：
外层循环for (int j = 0; j < s0.length(); ++j)遍历基准字符串s0的每个字符。
在每次迭代中，获取当前字符c = s0[j]。

内层循环遍历其他字符串：
内层循环for (const string& s : strs)遍历字符串数组中的每个字符串。
在内层循环中，首先检查当前字符的索引j是否超出了当前字符串s的长度。如果超出，说明公共前缀到此为止，返回s0.substr(0, j)，即从s0中提取到当前索引的子字符串。

字符匹配检查：
如果当前字符c与字符串s的对应字符s[j]不相等，说明公共前缀到此为止，同样返回s0.substr(0, j)。

返回结果：
如果外层循环完成，说明所有字符都匹配，返回整个基准字符串s0，即所有字符串的公共前缀就是s0。
```

### Ways
By C++:
```CPP
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        if (strs.empty()) return "";  // 检查空数组

        string s0 = strs[0];
        for (int j = 0; j < s0.length(); ++j) {
            char c = s0[j];
            for (const string& s : strs) {
                if (j == s.length() || s[j] != c) {
                    return s0.substr(0, j);
                }
            }
        }
        return s0;  // 如果循环结束，返回整个s0
    }
};
```
