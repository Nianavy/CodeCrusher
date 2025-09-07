# Question

[integer-to-roman](https://leetcode.cn/problems/integer-to-roman/)



# Answer

> solution

1.  **构建一个有序的查找表：核心数据结构。**
    *   创建两个数组（或等价的结构），一个存储罗马数字的数值 (`values`：1000, 900, 500, 400, ..., 1)，另一个存储其对应的罗马符号 (`reps`："M", "CM", "D", "CD", ..., "I")。
    *   **关键：** `values` 数组必须**按降序排列**。这是贪心算法能够正确工作的**绝对前提**。
    *   **更关键：** 这个数组必须**同时包含**标准表示 (如 100="C", 500="D") 和**所有特殊的减法表示** (如 900="CM", 400="CD", 90="XC", 40="XL", 9="IX", 4="IV")。并且，减法表示必须在其对应的标准表示之前。例如，`900` 必须在 `500` 和 `100` 之前，因为当我们处理 900 时，我们希望匹配 "CM"，而不是错误地匹配 "D" 然后 "CCCC" (这不符合罗马数字规则)。

2.  **贪婪地进行减法和拼接：主循环逻辑。**
    *   遍历查找表（从大到小）。
    *   对于每个 `value` 和 `rep` 对：
        *   使用一个**内层 `while` 循环**：只要当前的整数 `num` 大于或等于 `values[i]`，就执行以下操作：
            *   从 `num` 中减去 `values[i]` (`num -= values[i]`)。
            *   将对应的罗马符号 `reps[i]` 拼接到结果字符串中 (`res += reps[i]` 或 `strcat(res, reps[i]`)。
        *   这个 `while` 循环会持续进行，直到 `num` 小于当前的 `values[i]`，这意味着当前这个最大的罗马数字已经被“用尽”了。

3.  **结果返回：构建好的罗马数字字符串。**
    *   当外层 `for` 循环遍历完所有可能的罗马数字值后，`num` 最终会变为 `0` (如果初始输入有效)，并且 `res` 中包含了完整的罗马数字表示。

**总结：核心思路就是“先大后小，能减就减”。通过一个预先排好序的查找表，我们总是尝试用当前能表示的最大罗马数字去“消耗”整数值，直到整数归零。这既简单又高效，完美利用了罗马数字的表示特性。**

### Way Of C

> source code

```c
const int roman_values[] = {
    1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1
};

const char* roman_reps[] = {
    "M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"
};

const int NUM_ROMAN_SYMBOLS = sizeof(roman_values) / sizeof(roman_values[0]);

char* intToRoman(int num) {
    if (num <= 0 || num > 3999) return NULL;
    char *res = malloc(sizeof(char) * 16);
    if (res == NULL) {
        fprintf(stderr, "Error: malloc");
        exit(EXIT_FAILURE);
    }
    res[0] = '\0';
    for (int i = 0; i < NUM_ROMAN_SYMBOLS; ++i) {
        while (num >= roman_values[i]) {
            num -= roman_values[i];
            strcat(res, roman_reps[i]);
        }
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
    string intToRoman(int num) {
        int values[] = {
            1000,
            900, 500, 400, 100,
            90, 50, 40, 10,
            9, 5, 4, 1
        };
        string reps[] = {
            "M",
            "CM", "D", "CD", "C",
            "XC", "L", "XL", "X",
            "IX", "V", "IV", "I",
        };
        string res;
        for (int i = 0; i < 13; ++i) {
            while (num >= values[i]) {
                num -= values[i];
                res += reps[i];
            }
        }
        return res;
    }
};
```
