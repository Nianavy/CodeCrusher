https://www.acwing.com/problem/content/3531/

### Think
```
Simple pattern matching with [...] character sets
and case insensitivity to filter and output matching strings.
```

### Ways
```C++
#include <iostream>
#include <algorithm>
#include <cstring>

const int N = 1010;
int n;
std::string strs[N], p;

// 转为小写，实现不区分大小写匹配
std::string filter(const std::string& str) {
    std::string res;
    for (char c : str) {
        res += std::tolower(c);
    }
    return res;
}

// 检查字符串 a 是否匹配模式 p
bool match(const std::string& a, const std::string& p) {
    int i = 0, j = 0;  // i: a 的索引, j: p 的索引
    while (i < a.size() && j < p.size()) {
        if (p[j] != '[') {
            // 普通字符直接匹配
            if (a[i] != p[j]) {
                return false;
            }
            ++i;
            ++j;
        } 
        else {
            // 处理 [abc] 字符集
            ++j;  // 跳过 '['
            std::string charset;
            // 提取 ] 之前的所有字符
            while (j < p.size() && p[j] != ']') {
                charset += p[j];
                ++j;
            }
            // 检查是否找到 ']'
            if (j >= p.size()) {
                return false;  // 缺失 ']'，模式非法
            }
            ++j;  // 跳过 ']'

            // 检查当前字符 a[i] 是否在字符集中
            if (charset.find(a[i]) == std::string::npos) {
                return false;
            }
            ++i;
        }
    }
    // 必须完全匹配 a 和 p
    return i == a.size() && j == p.size();
}

int main() {
    std::cin >> n;
    for (int i = 0; i < n; ++i) {
        std::cin >> strs[i];
    }
    std::cin >> p;
    p = filter(p);  // 模式转小写

    for (int i = 0; i < n; ++i) {
        if (match(filter(strs[i]), p)) {
            std::cout << (i + 1) << ' ' << strs[i] << std::endl;
        }
    }

    return 0;
}
```
