# Question

[longest-common-prefix](https://leetcode.cn/problems/longest-common-prefix/)



# Answer

> solution

1.  **处理极端情况：空输入或空字符串。**
    *   如果输入的字符串向量/数组是空的 (`strs.empty()` 或 `strsSize == 0`)，那么显然没有公共前缀，直接返回空字符串。
    *   如果第一个字符串 (`strs[0]`) 本身就是空字符串，那么最长公共前缀也只能是空字符串。这个基准字符串的检查是必要的。

2.  **选择一个基准字符串：第一个字符串通常是方便的选择。**
    *   算法以 `strs[0]` 为基准，逐个检查其字符。最长公共前缀的长度不可能超过 `strs[0]` 的长度。

3.  **垂直扫描：外层循环逐字符前进。**
    *   使用一个外层循环（例如 `for (int i = 0; ; ++i)` 或 `while (true)`），`i` 代表当前正在比较的字符在所有字符串中的索引（即列号）。
    *   **终止条件：**
        *   如果当前列索引 `i` 已经超出了**基准字符串**的长度 (`i >= strs[0].size()` 或 `i >= strlen(strs[0])`)，说明基准字符串已经用完，公共前缀到此为止。
        *   **理由：** 如果基准字符串都用完了，那么就不可能再有更长的公共前缀了。

4.  **内层循环比较：检查所有其他字符串。**
    *   在每次外层循环中，首先取出基准字符串 `strs[0]` 在当前位置 `i` 的字符 `c`。
    *   然后，使用一个内层循环遍历**所有**字符串 (`strs` 中的每一个 `str`)。
    *   **短路退出机制：** 对于每一个 `str`，进行两个关键检查：
        *   `str.size() <= i` (或 `strlen(str) <= i`)：如果当前字符串 `str` 比当前公共前缀的长度 `i` 还短，那么它无法再继续延长公共前缀。
        *   `str[i] != c`：如果当前字符串 `str` 在位置 `i` 的字符与基准字符 `c` 不匹配。
        *   **如果上述任何一个条件为真，就意味着公共前缀已经到头了。立即停止所有循环，返回目前已经构建好的结果字符串。**

5.  **构建结果：如果所有都匹配，则扩展公共前缀。**
    *   如果内层循环顺利完成，没有触发任何短路退出，这意味着当前字符 `c` 在所有字符串的对应位置都匹配。
    *   将字符 `c` 追加到结果字符串 (`res`) 中 (`res += c;` 或 `res[i] = c; res[i+1] = '\0';`)。

**总结：核心思路就是“一列一列地检查，不匹配就滚蛋”。它利用了字符串前缀的顺序性，以最少的比较次数（O(N*L_min) 或 O(S)）快速找到结果。这种方法直接、高效，没有冗余的计算。**

### Way Of C

> source code

```c
char* longestCommonPrefix(char** strs, int strsSize) {
    if (strs == NULL || strsSize == 0) {
        char *empty_res = malloc(1);
        if (empty_res == NULL) {
            fprintf(stderr, "Error");
            exit(EXIT_FAILURE);
        }
        empty_res[0] = '\0';
        return empty_res;
    }

    if (strs[0] == NULL || *strs[0] == '\0') {
        char *empty_res = malloc(1);
        if (empty_res == NULL) {
            fprintf(stderr, "Error");
            exit(EXIT_FAILURE);
        }
        empty_res[0] = '\0';
        return empty_res;
    }

    int max_prefix_len = strlen(strs[0]);
    char *res = malloc((max_prefix_len + 1) * sizeof(char));
    if (res == NULL) {
        fprintf(stderr, "Error");
        exit(EXIT_FAILURE);
    }
    res[0] = '\0';

    for (int i = 0; ; ++i) {
        if (i >= strlen(strs[0])) return res;
        char c = strs[0][i];
        for (int j = 0; j < strsSize; ++j) {
            if (strs[j] == NULL) return res;
            if (strlen(strs[j]) <= i || strs[j][i] != c)
                return res;
        }
        res[i] = c;
        res[i + 1] = '\0';
    }

}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        string res;
        if (strs.empty()) return res;
        for (int i = 0; ; ++i) {
            if (i >= strs[0].size()) return res;
            char c = strs[0][i];
            for (auto &str: strs)
                if (str.size() <= i || str[i] != c)
                    return res;
            res += c;
        }
        return res;
    }
};
```
