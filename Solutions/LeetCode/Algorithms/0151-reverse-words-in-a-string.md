# Question

[reverse-words-in-a-string](https://leetcode.cn/problems/reverse-words-in-a-string/)



# Answer

> solution

**思路总结：**

**将问题分解为“整体反转” -> “单词反转” -> “单词间紧凑空格”三个独立、简单的原地操作，比单次遍历复杂状态机更健壮、更易调试。**

**具体步骤：**
1.  **整体反转**：把所有字符（包括空格）顺序翻转，此时单词顺序也反过来了。
2.  **单词反转**：遍历字符串，识别每个单词的起始和结束位置，单独反转每个单词，让单词本身恢复原样。
3.  **紧凑空格**：用双指针遍历，跳过前导空格和连续空格，只保留单词间的单个空格，并确保字符串不以空格结尾。对于C语言，推荐分配新内存构建干净字符串，避免修改 `argv[1]` 等不可写区域。

### Way Of C

> source code

```c
// 辅助函数：反转区间
static void reverse_range(char *s, int lo, int hi) {
    while (lo < hi) {
        char temp = s[lo];
        s[lo] = s[hi];
        s[hi] = temp;
        lo++;
        hi--;
    }
}
char* reverseWords(char* s) {
    if (!s) return NULL; // 防御性编程
    int len = strlen(s);
    if (len == 0) {
        // 返回一个空字符串。为了符合函数签名，创建一个空字符串
        char *result = (char*)malloc(1);
        if (result) result[0] = '\0';
        return result;
    }
    // 第一步：整体反转
    // 注意：不能直接修改 s，因为 s 可能指向常量区域 (argv[1])。
    // 我们必须在副本上操作。
    char *temp = (char*)malloc(len + 1);
    if (!temp) return NULL;
    memcpy(temp, s, len + 1); // 包括空终止符
    reverse_range(temp, 0, len - 1);
    // 第二步：反转每个单词 (在 temp 上)
    int word_start = 0;
    for (int i = 0; i <= len; i++) {
        if (i == len || temp[i] == ' ') {
            if (word_start < i) {
                reverse_range(temp, word_start, i - 1);
            }
            word_start = i + 1;
        }
    }
    // 第三步：清理空格 - 分配新内存并构建干净字符串
    // 不要在原 temp 上解析，因为它最后可能包含多余空格，需要紧凑化。
    // 我们可以找到干净字符串的理论最大长度，但为了简单，我们先扫描过程。
    
    // 先扫描，计算需要多少空间（为结果分配内存）
    int clean_len = 0;
    bool in_word = false;
    // 扫描 temp 来确定长度
    for (int i = 0; i < len; i++) {
        if (temp[i] != ' ') {
            clean_len++; // 加一个字符
            if (!in_word) {
                if (clean_len > 1) { // 不是第一个单词，要加空格
                    clean_len++; // 占一个空格位
                }
                in_word = true;
            }
        } else {
            in_word = false;
        }
    }
    // 但这样扫描两遍，如果 temp 本身有很多多余空格，干净长度 clean_len 会小于 len。
    // 实际上，我们正在做“一次遍历构建”的事情。让我们直接用temp作为源，在同一个循环里做构建。
    // 分配结果内存。最大可能长度就是 len（如果没有多余空格），但为了安全，我们分配 len+1。
    char *result = (char*)malloc(len + 1);
    if (!result) {
        free(temp);
        return NULL;
    }
    // 现在，用 temp 的内容构建 result
    int write = 0;
    bool need_space = false;
    bool any_word = false; // 是否写过单词
    for (int i = 0; i < len; i++) {
        if (temp[i] != ' ') {
            // 如果需要写空格，且不是第一个单词
            if (any_word && need_space) {
                result[write++] = ' ';
                need_space = false;
            }
            result[write++] = temp[i];
            any_word = true;
            // 遇到单词字符后，我们“尚未确定”是否需要空格。
            // 下次遇到空格，才设置 need_space 为 true。
        } else {
            // 遇到空格。
            // 如果之前是单词（any_word 为 true），说明我们需要在下一个单词前加空格。
            // 但 `need_space` 可能已经被设置，也可能没有，所以我们只在单词结束时设置一次。
            if (any_word) {
                need_space = true;
            }
        }
    }
    result[write] = '\0'; // 确保空终止
    free(temp);
    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    string reverseWords(string s) {
        // 第一步：整体反转（处理单词顺序）
        reverse(s.begin(), s.end());
        
        // 第二步：反转每个单词（恢复单词本身）
        int word_start = 0;
        for (int i = 0; i <= s.size(); i++) {
            if (i == s.size() || s[i] == ' ') {
                if (word_start < i) { // 有单词
                    reverse(s.begin() + word_start, s.begin() + i);
                }
                word_start = i + 1;
            }
        }

        // 第三步：清理所有多余空格 - 使用双指针在原地构建紧凑版本
        // 核心思想：读指针 `r` 扫描，写指针 `w` 收集非空格字符和必要的单个空格。
        int w = 0;
        // 使用一个标志位来跟踪是否已经写入了一个单词，用于决定是否添加空格
        bool has_written_word = false;
        
        for (int r = 0; r < s.size(); ++r) {
            if (s[r] != ' ') {
                // 遇到非空格字符（属于某个单词）
                if (has_written_word && (w > 0 && s[w-1] != ' ')) {
                    // 如果之前写过单词，且写位置的前一个字符不是空格（即新单词开始）
                    // 则需要在新单词前添加一个空格。但更简单的方式是，只在上一个单词后直接加空格。
                    // 所以，我们只在 `has_written_word` 为 true 且我们开始一个新单词时，才提前加空格。
                    // 但这个判断很难在“遇到单词字符时”提前做。让我们调整策略。
                }
            }
        }
        // 又想复杂了。让我们用最简单、最不会错的那个循环：

        // 最终第三步：清理空格的标准双指针压缩 (带回溯)
        // 1. 处理前导空格
        int read = 0;
        while (read < s.size() && s[read] == ' ') read++;
        if (read == s.size()) {
            s.resize(0);
            return s;
        }
        // 2. 开始处理
        int write = 0;
        bool in_word = false;
        for (int i = read; i < s.size(); ++i) {
            if (s[i] != ' ') {
                s[write++] = s[i];
                in_word = true;
            } else {
                // 遇到空格，只有在之前处于单词内部时，才需要保留一个空格
                if (in_word) {
                    s[write++] = ' ';
                    in_word = false;
                    // 注意：这里可能连续放入多个空格，但我们只在单词后第一个空格时放入，
                    // 之后的连续空格 `in_word` 为 false，不会进入这里。
                }
                // 如果 `in_word` 已经是 false，说明是连续空格，跳过。
            }
        }
        // 3. 后处理：如果最后一个字符是空格（因为写入了空格），则移除它
        if (write > 0 && s[write - 1] == ' ') {
            write--;
        }
        s.resize(write);
        return s;
    }
};
```
