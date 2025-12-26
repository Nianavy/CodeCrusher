# Question

[valid-palindrome](https://leetcode.cn/problems/valid-palindrome/)



# Answer

> solution

预处理→归一化→双指针

只用一次遍历，把“去掉垃圾字符、忽略大小写”统统当场搞定；剩下的就是单纯判断回文。

### Way Of C

> source code

```c
bool isPalindrome(const char *s) {
    const unsigned char *p = (const unsigned char *)s;
    const unsigned char *q = p;
    while (*q) ++q;
    --q;
    for (; p < q; ++p, --q) {
        while (p < q && !isalnum(*p)) ++p;
        while (p < q && !isalnum(*q)) --q;
        if (p >= q) break;
        if (tolower(*p) != tolower(*q)) return false;
    }
    return true;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    bool isPalindrome(string s) {
        int l = 0, r = (int)s.size() - 1;
        const auto ok = [](unsigned char c) {
            return ('0' <= c && c <= '9') || ('a' <= c && c <= 'z') ||
                   ('A' <= c && c <= 'Z');
        };
        const auto down = [](unsigned char c) {
            return (c >= 'A' && c <= 'Z') ? c + 32 : c;
        };
        while (l < r) {
            while (l < r && !ok(s[l])) ++l;
            while (l < r && !ok(s[r])) --r;
            if (l < r && down(s[l]) != down(s[r])) return false;
            ++l, --r;
        }
        return true;
    }
};
```
