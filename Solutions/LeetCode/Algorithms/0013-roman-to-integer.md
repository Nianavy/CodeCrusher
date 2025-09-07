# Question

[roman-to-integer](https://leetcode.cn/problems/roman-to-integer/)



# Answer

> solution


1.  **构建一个字符到整数的查找表：核心数据结构。**
    *   为了快速将罗马字符 (如 'I', 'V', 'X') 映射到它们的整数值 (1, 5, 10)，需要一个查找表。
    *   在 C++ 中，`std::unordered_map<char, int>` 是最便捷和高效的选择。
    *   在纯 C 中，一个 `switch` 语句是等效且通常更高效的选择，因为它避免了哈希表的运行时开销。
    *   **理由：** 每次 O(1) 的查找，确保了整体算法的效率。

2.  **从左到右遍历字符串：处理每个字符。**
    *   使用一个循环，从字符串的第一个字符开始，一直遍历到倒数第二个字符（因为需要检查 `i + 1`）。
    *   **为什么从左到右？** 罗马数字的规则决定了，减法表示（如 "IV"）中的减数 (`I`) 总是出现在被减数 (`V`) 的**左边**。所以，从左到右遍历可以让我们“预见”下一个字符，从而判断当前字符是否参与减法。

3.  **核心逻辑：判断当前字符是加还是减。**
    *   在循环的每一步 `i`，获取当前字符 `s[i]` 的值和下一个字符 `s[i+1]` 的值。
    *   **关键条件：** `if (s[i] 的值 < s[i+1] 的值)`
        *   **如果条件为真 (例如 "IV" 中处理 'I')：** 这意味着 `s[i]` 是一个减数。将其值从最终结果中**减去** (`res -= value_of_s_i;`)。
        *   **如果条件为假 (例如 "VI" 中处理 'V' 或 'I')：** 这意味着 `s[i]` 是一个加数。将其值加到最终结果中 (`res += value_of_s_i;`)。
    *   **特别处理最后一个字符：** 循环到倒数第二个字符时，会检查最后一个字符。当循环结束时，**最后一个字符的值总是被加到结果中**，因为它不可能再是任何减法表示的减数。你的代码通过 `else res += hash[s[i]];` 优雅地处理了这一点，因为当 `i + 1` 不再小于 `s.size()` 时，`if` 条件为假，就会执行 `else` 分支。

4.  **累积结果：**
    *   `res` 变量从 `0` 开始，在遍历过程中不断累加或累减，最终得到整数结果。

**总结：核心思路就是“右大则减，否则加”。通过单次遍历，利用局部信息（当前字符和下一个字符的关系）来决定如何累积值，直接且高效地解析了罗马数字的全部规则。**

### Way Of C

> source code

```c
int roman_char_to_int(char c) {
    switch (c) {
        case 'I': return 1;
        case 'V': return 5;
        case 'X': return 10;
        case 'L': return 50;
        case 'C': return 100;
        case 'D': return 500;
        case 'M': return 1000;
        default: return 0;
    }
}

int romanToInt(char* s) {
    if (s == NULL || *s == '\0') return 0;
    int res = 0;
    int n = strlen(s);
    for (int i = 0; i < n; ++i) {
        int current_val = roman_char_to_int(s[i]);
        if (i + 1 < n && current_val < roman_char_to_int(s[i + 1]))
            res -= current_val;
        else res += current_val;
    }
    return res;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int romanToInt(string s) {
        unordered_map<char, int> hash;
        hash['I'] = 1, hash['V'] = 5;
        hash['X'] = 10, hash['L'] = 50;
        hash['C'] = 100, hash['D'] = 500;
        hash['M'] = 1000;

        int res = 0;
        for (int i = 0; i < s.size(); ++i) {
            if (i + 1 < s.size() && hash[s[i]] < hash[s[i + 1]])
                res -= hash[s[i]];
            else res += hash[s[i]];
        }
        
        return res;
    }
};
```
