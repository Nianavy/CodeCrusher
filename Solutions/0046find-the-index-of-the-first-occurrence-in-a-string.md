https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string

### Think
```TXT
strStr 方法：
这是一个静态方法，接受两个字符串 s1（主串）和 s2（模式串）。
该方法调用 kmp 函数来执行 KMP 算法。

kmp 方法：
输入参数为主串 s1 和模式串 s2。
计算主串和模式串的长度 n 和 m。
创建 next 数组，存储模式串的部分匹配信息。
使用两个指针 x 和 y，分别遍历主串和模式串。

通过比较 s1[x] 和 s2[y] 来判断字符是否匹配：
如果匹配，两个指针同时向后移动。
如果不匹配且 y 为 0，说明模式串的第一个字符不匹配，主串指针 x 向后移动。
如果不匹配且 y 不为 0，使用 next 数组调整模式串指针 y，避免重复比较。

nextArray 方法：
生成模式串的 next 数组，用于记录每个位置的最长相等前后缀的长度。
通过两个指针 i 和 cn 来构建 next 数组：
i 表示当前要求 next 值的位置。
cn 表示当前要和前一个字符比对的下标。
根据字符是否匹配来更新 next 数组。
```

### Ways
By C++:
```C++
class Solution {
public:
static int strStr(const std::string& s1, const std::string& s2) {
        // return s1.find(s2);
        return kmp(s1, s2);
    }

    // KMP算法
    static int kmp(const std::string& s1, const std::string& s2) {
        int n = s1.size(), m = s2.size(), x = 0, y = 0;
        // O(m)
        std::vector<int> next = nextArray(s2, m);
        // O(n)
        while (x < n && y < m) {
            if (s1[x] == s2[y]) {
                x++;
                y++;
            } else if (y == 0) {
                x++;
            } else {
                y = next[y];
            }
        }
        return y == m ? x - y : -1;
    }

    // 得到next数组
    static std::vector<int> nextArray(const std::string& s, int m) {
        if (m == 1) {
            return {-1};
        }
        std::vector<int> next(m);
        next[0] = -1;
        next[1] = 0;
        // i表示当前要求next值的位置
        // cn表示当前要和前一个字符比对的下标
        int i = 2, cn = 0;
        while (i < m) {
            if (s[i - 1] == s[cn]) {
                next[i++] = ++cn;
            } else if (cn > 0) {
                cn = next[cn];
            } else {
                next[i++] = 0;
            }
        }
        return next;
    }
};
```
