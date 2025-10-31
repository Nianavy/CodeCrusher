# Question

[add-binary](https://leetcode.cn/problems/add-binary/)



# Answer

> solution

### **解决思路总结：统一求和与反向构造法**

**格言：** “不要试图为每一个位组合编写逻辑。把它们视为数字，让算术去处理进位。”

**核心思想：**
二进制加法是典型的从低位到高位的操作，涉及到当前位数字、另一字符串的当前位数字以及来自前一位的进位（`carry`）。我们将所有这些值简单地相加，然后利用整数除法和取模来确定结果和新的进位。

1.  **统一迭代：**
    *   使用两个指针 `i` 和 `j` 分别指向字符串 $A$ 和 $B$ 的末尾。
    *   主循环条件设置为：`while (i >= 0 || j >= 0 || carry > 0)`。只要任一字符串还有数字，或者前一位计算仍产生进位，循环就必须继续。这优雅地处理了不等长字符串和最终进位扩容的情况。

2.  **计算与转移：**
    在循环的每一步，执行以下操作：
    *   **累加到 `carry`：** 将当前指针 `i` 和 `j` 对应的数字（如果指针未越界）累加到 `carry` 变量中。如果指针越界，则贡献为 0。
    *   **结果位：** 当前的结果位是 `carry % 2`（二进制求和的结果）。将这个结果位转换为字符并存储起来。
    *   **新进位：** 下一轮的进位是 `carry / 2`。

3.  **构造与反转：**
    *   由于我们是从字符串的末尾（最低位）开始计算，结果字符串是以反向顺序构造的（从低位到高位）。
    *   为了得到最终正确的二进制字符串（从高位到低位），在循环结束后，需要对结果字符串进行一次 O(N) 的**反转**操作。

**实用主义的体现：**
该方法通过将字符转化为整数进行统一求和，避免了冗长且易错的 `if/else` 逻辑（例如：判断 `a[i]=='1'` 和 `b[j]=='0'` 并且 `carry==1` 的复杂组合）。整个加法逻辑被简化为 `carry += digit`，`result = carry % 2` 和 `carry = carry / 2`，体现了简洁和高效的数学美感。

### Way Of C

> source code

```c
char* addBinary(char* a, char* b) {
    int len1 = strlen(a);
    int len2 = strlen(b);
    // 结果的最大长度为 max(len1, len2) + 1，需要额外空间存 '\0'
    int max_len = len1 > len2 ? len1 : len2;
    int result_len = 0; // 实际结果的长度

    // 先分配最大可能长度 + 1 空间
    char *result_reversed = (char*)malloc((max_len + 2) * sizeof(char));
    if (result_reversed == NULL) {
        return NULL;
    }

    int i = len1 - 1;
    int j = len2 - 1;
    int carry = 0;

    // 循环条件：只要任一字符串未遍历完，或仍有进位
    while (i >= 0 || j >= 0 || carry > 0) {
        // 1. 获取当前位的数值并累加到 carry
        if (i >= 0) {
            carry += a[i] - '0';
            i--;
        }
        if (j >= 0) {
            carry += b[j] - '0';
            j--;
        }

        // 2. 计算结果位 (carry % 2) 并添加到结果数组
        result_reversed[result_len++] = (carry % 2) + '0';

        // 3. 计算新的进位 (carry / 2)
        carry /= 2;
    }

    // 4. 反转结果：先分配最终的正确大小的空间
    char *final_result = (char*)malloc((result_len + 1) * sizeof(char));
    if (final_result == NULL) {
        free(result_reversed);
        return NULL;
    }
    
    // 5. 将反向存储的结果复制并反转到最终数组
    final_result[result_len] = '\0';
    for (int k = 0; k < result_len; k++) {
        final_result[k] = result_reversed[result_len - 1 - k];
    }

    free(result_reversed);
    return final_result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    std::string addBinary(std::string a, std::string b) {
        std::string result = "";
        int i = a.length() - 1;
        int j = b.length() - 1;
        int carry = 0;

        // 循环继续条件：只要任一字符串未遍历完，或仍有进位
        while (i >= 0 || j >= 0 || carry > 0) {
            // 1. 获取当前位的数值并累加到 carry
            if (i >= 0) {
                carry += a[i] - '0';
                i--;
            }
            if (j >= 0) {
                carry += b[j] - '0';
                j--;
            }

            // 2. 计算结果位 (carry % 2) 并添加到结果
            result.push_back((carry % 2) + '0');

            // 3. 计算新的进位 (carry / 2)
            carry /= 2;
        }

        // 结果是反向存储的，需要反转
        std::reverse(result.begin(), result.end());
        
        return result;
    }
};
```
