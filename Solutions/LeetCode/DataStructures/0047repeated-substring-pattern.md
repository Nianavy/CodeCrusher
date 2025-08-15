https://leetcode.cn/problems/repeated-substring-pattern

### Think
```TXT
KMP算法的核心部分：
kmp函数接收两个字符串：query（待匹配的字符串）和pattern（模式字符串）。
首先，计算模式字符串的失败函数（fail数组），用于在匹配失败时快速回溯。

失败函数的构建：
fail[i]表示模式字符串pattern中，长度为i+1的前缀和后缀的最长公共元素的长度。
通过双指针的方式，遍历模式字符串，构建fail数组。

字符串匹配：
使用match变量来记录当前匹配的模式字符串的索引。
遍历query字符串，使用fail数组来处理匹配失败的情况。
如果找到完整的模式字符串，则返回true，表示匹配成功。

判断重复子串：
repeatedSubstringPattern函数通过将字符串s与自身拼接（s + s）来判断是否存在重复子串。
由于拼接后的字符串中，原字符串的重复模式会在中间部分出现，因此只需调用kmp函数进行匹配。
```

### Ways
By C++:
```C++
class Solution {
public:
    bool kmp(const string& query, const string& pattern) {
        int n = query.size();
        int m = pattern.size();
        vector<int> fail(m, -1);
        for (int i = 1; i < m; ++i) {
            int j = fail[i - 1];
            while (j != -1 && pattern[j + 1] != pattern[i])
                j = fail[j];
            if (pattern[j + 1] == pattern[i])
                fail[i] = j + 1;
        }
        int match = -1;
        for (int i = 1; i < n - 1; ++i) {
            while (match != -1 && pattern[match + 1] != query[i])
                match = fail[match];
            if (pattern[match + 1] == query[i]) {
                ++match;
                if (match == m - 1)
                    return true;
            }
        }
        return false;
    }
    bool repeatedSubstringPattern(string s) { return kmp(s + s, s); }
};
```
