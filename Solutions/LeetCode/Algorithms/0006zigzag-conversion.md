# Question

[zigzag-conversion](https://leetcode.cn/problems/zigzag-conversion/)



# Answer

> solution

### **按行模拟 / 逐行读取 (Row-by-Row Simulation/Reading)**

**核心思想：**
最终的 Z 字形字符串是按照“行”拼接起来的。也就是说，我们先收集第一行的所有字符，然后收集第二行的所有字符，依此类推，直到收集完最后一行的所有字符。

1.  **观察 Z 字形模式：**
    以 `numRows = 4`，字符串 `PAYPALISHIRING` 为例：
    ```
    P     I     N
    A   L S   I G
    Y A   H R
    P     I
    ```
    最终结果是 `PINALSIGYAHRPI`。

    仔细看，字符在 Z 字形中是**周期性**出现的。一个完整的 Z 字形“周期”是从顶到底再从底到顶。
    *   `P` -> `A` -> `Y` -> `P` (下降)
    *   `I` -> `L` (上升)
    *   `S` -> `H` (下降)
    *   `I` -> `R` (上升)
    *   ...

2.  **计算周期长度：**
    一个完整的 Z 字形周期有多少个字符？
    *   从第一行到第 `numRows` 行（下降）：`numRows` 个字符。
    *   从第 `numRows-1` 行回到第二行（上升）：`numRows - 2` 个字符。
    *   因此，一个完整的周期包含 `numRows + (numRows - 2) = 2 * numRows - 2` 个字符。
    *   例如，`numRows = 4`，`cycle_len = 2 * 4 - 2 = 6`。
        `P A Y P A L` 是一个周期，长度为 6。
        `P` (idx 0), `A` (idx 1), `Y` (idx 2), `P` (idx 3), `A` (idx 4), `L` (idx 5)

3.  **逐行收集字符：**
    对于每一行 `i` (从 0 到 `numRows-1`)：

    *   **第一行 (`i = 0`)：**
        所有字符都出现在周期的“下降”部分顶部。它们的索引是 `0`, `cycle_len`, `2 * cycle_len`, `3 * cycle_len`, ...
        通用公式：`j = i + k * cycle_len`
        当 `i = 0` 时，`j = k * cycle_len`

    *   **最后一行 (`i = numRows - 1`)：**
        所有字符都出现在周期的“下降”部分底部。它们的索引是 `numRows - 1`, `(numRows - 1) + cycle_len`, ...
        通用公式：`j = i + k * cycle_len`
        当 `i = numRows - 1` 时，`j = (numRows - 1) + k * cycle_len`

    *   **中间行 (`0 < i < numRows - 1`)：**
        这些行最复杂，因为字符会**出现两次**在一个周期中：一次在下降路径上，一次在上升路径上。
        1.  **下降路径上的字符：** 索引为 `i`, `i + cycle_len`, `i + 2 * cycle_len`, ...
            通用公式：`pos1 = i + k * cycle_len`
        2.  **上升路径上的字符：** 索引为 `(cycle_len - i)`, `(cycle_len - i) + cycle_len`, `(cycle_len - i) + 2 * cycle_len`, ...
            通用公式：`pos2 = (k * cycle_len) + (cycle_len - i)`
            等价于：`pos2 = pos1 + (cycle_len - 2 * i)`
            也就是说，在下降路径的 `pos1` 之后，下一个是上升路径的 `pos2`，这个 `pos2` 的位置距离 `pos1` 对应的下一个下降周期起始点 (`pos1 + cycle_len`) 有一个偏移量 `2 * i`。

4.  **算法步骤总结：**
    a.  计算 `cycle_len = 2 * numRows - 2`。
    b.  创建一个空的 `result` 字符串。
    c.  **外层循环：** 遍历 `i` 从 `0` 到 `numRows - 1` (代表每一行)。
    d.  **内层循环：** 遍历 `j` 从 `i` 开始，每次递增 `cycle_len`，直到 `j` 超出字符串长度。
        i.  将 `s[j]` 加入 `result`。
        ii. 如果 `i` 是中间行 (不是第一行也不是最后一行)，计算第二个字符的索引 `k = j + cycle_len - 2 * i`。
        iii. 如果 `k` 在字符串范围内，将 `s[k]` 加入 `result`。
    e.  返回 `result`。

**优点：**
*   **直观：** 直接按照输出顺序构造结果，容易理解。
*   **效率：** 每个字符只被访问一次，每个字符最多被添加到结果两次 (下降/上升)，因此时间复杂度为 O(N)，空间复杂度为 O(N)。

**缺点：**
*   中间行的索引计算稍微复杂，需要仔细推导。

### Way Of C

> source code

```c
char* convert(char* s, int numRows) {
    if (s == NULL) return NULL;
    int s_len = strlen(s);
    if (numRows == 1 || s_len == 0 || s_len < numRows) {
        char *res_copy = malloc(s_len + 1);
        if (res_copy == NULL) {
            perror("malloc for result copy");
            return NULL;
        }
        strcpy(res_copy, s);
        return res_copy;
    }
    char *result = malloc(s_len + 1);
    if (result == NULL) {
        perror("malloc for result string");
        return NULL;
    }
    result[0] = '\0';
    int cycle_len = 2 * numRows - 2;
    for (int i = 0; i < numRows; ++i) {
        for (int j = i; j < s_len; j += cycle_len) {
            char temp_char[2];
            temp_char[0] = s[j];
            temp_char[1] = '\0';
            strcat(result, temp_char);
            if (i != 0 && i != numRows - 1) {
                int k = j + cycle_len - 2 * i;
                if (k < s_len) {
                    temp_char[0] = s[k];
                    temp_char[1] = '\0';
                    strcat(result, temp_char);
                }
            }
        }
    }
    return result;
}
```

---


### Way Of C++

> source code

```c++
class Solution {
public:
    string convert(string s, int numRows) {
        if (numRows == 1 || s.empty() || s.size() <= numRows) return s;
        string res;
        res.reserve(s.size());
        int cycle_len = 2 * numRows - 2;
        for (int i = 0; i < numRows; ++i) {
            for (int j = i; j <s.size(); j += cycle_len) {
                res += s[j];
                if (i != 0 && i != numRows - 1) {
                    int k = j + cycle_len - 2 * i;
                    if (k < s.size()) res += s[k];
                }
            }
        }
        return res;
    }
};
```
