# Question

[minimum-window-substring](https://leetcode.cn/problems/minimum-window-substring/)



# Answer

> solution

最小覆盖子串

### 1. 核心挑战与策略

*   **挑战**：在 $O(N^2)$ 的暴力查找中，重复的子串检查是瓶颈。我们需要一种 $O(1)$ 查找子串是否满足条件的方法。
*   **策略**：**滑动窗口 (Sliding Window)**。我们用两个指针 $L$ 和 $R$ 维护一个窗口，并用一个计数器跟踪窗口内字符是否满足 $T$ 的要求。

### 2. 关键数据结构：高效计数器 (欠款法)

我们使用一个固定大小的数组（例如 256 大小，假设 ASCII）作为频率计数器，这是比哈希表更快的选择。

*   **初始化**：计数器 `count` 存储 $T$ 中所有字符的**需求量**（即 $T$ 的频率）。
*   **核心技巧（欠款法）**：
    *   在窗口扩展时，我们将 $S[R]$ 的 `count` **递减**。
    *   当 `count[char] >= 0` 时，意味着我们**命中**了 $T$ 中一个必需的字符。
    *   当 `count[char] < 0` 时，意味着这个字符是多余的，或不属于 $T$。

### 3. 两阶段滑动窗口流程

整个过程由两个主要阶段驱动，它们交替进行，确保 $L$ 和 $R$ 每个索引只被访问一次，保证 $O(N)$ 复杂度。

#### 阶段 I：窗口扩展 (R 指针移动)

*   **目标**：向右移动 $R$ 指针，直到窗口 $[L, R]$ 完整覆盖 $T$ 中所有字符（即 `matched_count == |T|`）。
*   **动作**：
    1.  $R$ 移动，对 $S[R]$ 的 `count` 减 1。
    2.  如果 $S[R]$ 是 $T$ 中所需字符 (`count[S[R]] \ge 0$)，则 `matched_count` 增加。

#### 阶段 II：窗口收缩与优化 (L 指针移动)

*   **目标**：一旦窗口满足覆盖条件 (`matched_count == |T|`)，尝试从左边收缩 $L$，找到最小子串，直到覆盖条件被破坏。
*   **动作**：
    1.  记录当前的最小长度和起始位置。
    2.  $L$ 移动，对 $S[L]$ 的 `count` 加 1（**归还欠款**）。
    3.  如果归还后 `count[S[L]] > 0$，意味着 $S[L]$ 是 $T$ 中必需的字符，**且现在窗口内已经没有足够的该字符了**，因此 `matched_count` 递减。
    4.  收缩循环结束，返回阶段 I 继续扩展 $R$。

### 4. C 语言的实用主义考量

*   **内存安全**：C 版本的函数返回类型必须是**动态分配的** `char*`。无论是返回找到的子串还是返回空字符串 `""`（当未找到时），都必须确保它们是通过 `malloc` 分配的（即使是长度为 1 的空字符串），以便调用者可以统一调用 `free()`，避免内存泄漏或混淆。

### Way Of C

> source code

```c
/**
 * 最小覆盖子串 (Min Window Substring)
 * 修正了内存泄漏和不一致的返回类型问题。
 * @return 动态分配的最小子串，如果未找到则返回动态分配的空字符串 ""。
 */
char* minWindow(char* s, char* t)
{
    int slen = strlen(s);
    int tlen = strlen(t);
    
    if (tlen == 0 || slen < tlen) {
        // 返回一个动态分配的空字符串，以便调用者可以统一 free()
        char *empty_res = (char*)malloc(1);
        if (empty_res) *empty_res = '\0';
        return empty_res;
    }

    // 使用数组作为计数器，效率最高
    int count[256] = { 0 };
    for (int i = 0; i < tlen; i++) {
        count[(unsigned char)t[i]]++;
    }

    int L = 0, R = 0;
    int matched_count = 0;
    int min_len = INT_MAX;
    int min_start = 0;

    while (R < slen) {
        unsigned char char_r = (unsigned char)s[R];
        
        // 扩展 R
        if (--count[char_r] >= 0) {
            matched_count++;
        }

        // 尝试收缩 L
        while (matched_count == tlen) {
            if (R - L + 1 < min_len) {
                min_len = R - L + 1;
                min_start = L;
            }

            unsigned char char_l = (unsigned char)s[L];

            // 移动 L，归还字符
            if (++count[char_l] > 0) {
                matched_count--;
            }
            L++;
        }
        R++;
    }

    // 构造结果字符串
    if (min_len <= slen) {
        char *result = (char*)malloc(min_len + 1);
        if (!result) return NULL; // 内存分配失败
        
        memcpy(result, s + min_start, min_len);
        result[min_len] = '\0';
        return result;
    } else {
        // 未找到，返回一个动态分配的空字符串
        char *empty_res = (char*)malloc(1);
        if (empty_res) *empty_res = '\0';
        return empty_res;
    }
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    string minWindow(string s, string t) {
        if (t.empty() || s.length() < t.length()) {
            return "";
        }

        // 使用 256 大小数组作为字符频率计数器 (假设 ASCII/扩展 ASCII)
        vector<int> required_counts(256, 0);
        for (char c : t) {
            required_counts[c]++;
        }

        int L = 0, R = 0;
        int matched_count = 0; // 记录匹配 t 字符的个数
        int min_len = INT_MAX;
        int min_start = 0;

        while (R < s.length()) {
            char char_r = s[R];

            // 1. 扩展窗口右边界 R
            if (--required_counts[char_r] >= 0) {
                // 如果这个字符在 T 中是需要的 (计数 >= 0)，则匹配数增加
                matched_count++;
            }

            // 2. 尝试收缩窗口左边界 L
            while (matched_count == t.length()) {
                // 找到一个覆盖子串，更新最小长度
                if (R - L + 1 < min_len) {
                    min_len = R - L + 1;
                    min_start = L;
                }

                char char_l = s[L];
                
                // 移动 L，将字符归还到 required_counts
                if (++required_counts[char_l] > 0) {
                    // 如果归还后计数 > 0，说明这个字符是 T 中必需的，匹配数递减
                    matched_count--;
                }
                L++;
            }
            R++;
        }

        return min_len == INT_MAX ? "" : s.substr(min_start, min_len);
    }
};
```
