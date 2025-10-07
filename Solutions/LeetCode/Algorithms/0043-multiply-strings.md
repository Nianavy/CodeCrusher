# Question

[multiply-strings](https://leetcode.cn/problems/multiply-strings/)



# Answer

> solution

解决“大数相乘”问题的最佳实践是：**使用一个整数数组（或向量）来存储中间结果，分两阶段处理：先累加乘积，再处理进位，最后格式化输出。**

1.  **第一步永远是简化数据结构。**
    *   **思想：** 避免直接在字符串上操作，因为字符的加法和进位处理复杂。使用整数数组来存储每一位的结果（可能是大于9的临时值）。
    *   **实现：** 创建一个 `int` 数组 `product_arr`，其大小为 `len1 + len2`。这是因为两个 `N` 位数相乘，结果最多是 `2N` 位数。例如 `9 * 9 = 81` (1+1=2位)；`99 * 99 = 9801` (2+2=4位)。`calloc` 初始化为0是好的，方便累加。

2.  **消除所有特殊情况，将它们融入通用逻辑。**
    *   **"0" 乘以任何数：** 这是最常见的特殊情况。如果 `num1` 或 `num2` 中任意一个是 "0"，结果直接就是 "0"。在函数开头就处理，避免不必要的计算，这是最实用的。
    *   **空字符串：** 虽然用户通常会提供有效数字字符串，但如果出现空字符串，也应视作 "0" 处理或报错。我们在这里选择了检查 `strcmp(num1, "0") == 0`，这涵盖了数字为0的情况。

3.  **用最直接、最清晰（即使有时看起来“笨拙”）的方式实现。**
    *   **核心乘法与累加 (`O(len1 * len2)`)：**
        *   遍历 `num1` 的每一位 (`digit1`) 和 `num2` 的每一位 (`digit2`)。
        *   将 `digit1 * digit2` 的结果累加到 `product_arr` 中的正确位置。
        *   **关键索引：** 如果 `num1[i]` 和 `num2[j]` 是从高位到低位索引（即字符串中的正常顺序），那么 `num1[i] * num2[j]` 的结果会影响到 `product_arr[i + j]` 和 `product_arr[i + j + 1]`。为了避免复杂的进位处理，我们通常将 `product_arr[i + j + 1]` 作为当前位，`product_arr[i + j]` 作为高一位。
            *   `product_arr[i + j + 1] += (num1[i] - '0') * (num2[j] - '0');`
            *   这种累加方式是正确的，它允许同一个 `product_arr` 位置积累多个乘积项，而进位处理则在稍后统一进行。
    *   **统一进位处理 (`O(len1 + len2)`)：**
        *   在所有乘法和累加完成后，从 `product_arr` 的最低位（右侧）向最高位（左侧）遍历。
        *   维护一个 `carry` 变量。
        *   对于 `product_arr` 的每一位 `i`，计算 `sum = product_arr[i] + carry`。
        *   将 `sum % 10` 存储为当前位的结果，并将 `sum / 10` 更新为新的 `carry`。
        *   将这些处理后的数字转换成字符 (`+'0'`) 存储到最终的结果字符串中。
    *   **去除前导零 (`O(len1 + len2)`)：**
        *   遍历最终结果字符串，找到第一个非 '0' 的字符。
        *   如果所有字符都是 '0' (除了结果是 "0" 的情况已经被处理)，或者没有前导零，则直接返回。
        *   如果有前导零，截取从第一个非 '0' 字符开始的子字符串。

4.  **确保零破坏性，向后兼容是最高优先级。**
    *   C 语言版本：使用 `malloc` 分配结果字符串内存。**调用者必须负责 `free` 这块内存**，这是 C 语言的规矩。我们已在 `main` 函数中展示了正确的 `free` 操作。
    *   C++ 版本：使用 `std::string`，其内存管理由 C++ 标准库自动处理，无需手动 `free`。

**总结来说，大数相乘的核心思路就是将大数问题分解为小数字的乘法和加法，通过一个中间数组来精确模拟小学竖式乘法的每一个步骤，并通过两阶段（累加乘积、处理进位）确保正确性。最终的格式化处理（去除前导零）是用户体验的关键。**

### Way Of C

> source code

```c
char* multiply(char* num1, char* num2) {
    // 1. 特殊情况处理：任何一个数是 "0"，结果就是 "0"
    if (strcmp(num1, "0") == 0 || strcmp(num2, "0") == 0) {
        char* zero_res = (char*)malloc(sizeof(char) * 2);
        if (zero_res == NULL) {
            fprintf(stderr, "Memory allocation failed\n");
            exit(EXIT_FAILURE); // 内存分配失败是严重问题
        }
        strcpy(zero_res, "0");
        return zero_res;
    }

    int len1 = strlen(num1);
    int len2 = strlen(num2);
    // 结果最大长度是 len1 + len2。例如 "99" * "99" = "9801" (2+2=4位)
    // 存储中间结果的数组，多一位用于进位，再多一位用于 '\0'
    int result_len = len1 + len2;
    // 使用 int 数组存储数字，方便计算
    int* product_arr = (int*)calloc(result_len, sizeof(int)); // calloc 会初始化为 0
    if (product_arr == NULL) {
        fprintf(stderr, "Memory allocation failed\n");
        exit(EXIT_FAILURE);
    }

    // 2. 核心乘法和累加
    // 模拟竖式乘法，num1 的每一位乘以 num2 的每一位
    // num1[i] * num2[j] 的结果会累加到 product_arr 的 i+j+1 索引位置 (从右往左数，0是最低位)
    // 例如 num1="12", num2="34"
    // (2*4) -> product_arr[0] (个位)
    // (1*4 + 2*3) -> product_arr[1] (十位)
    // (1*3) -> product_arr[2] (百位)
    // 因为字符串是高位在前，所以我们从右往左遍历 (即从低位到高位)
    for (int i = len1 - 1; i >= 0; i--) {
        int digit1 = num1[i] - '0';
        for (int j = len2 - 1; j >= 0; j--) {
            int digit2 = num2[j] - '0';
            // 当前乘积累加到对应的位置
            // 这里的索引 i+j+1 是基于结果字符串的索引（从左往右，0是最高位）
            // 例如 len1=2, len2=2. i=1,j=1 => i+j+1=3. product_arr[3] 对应个位
            // i=1,j=0 => i+j+1=2. product_arr[2] 对应十位
            // i=0,j=1 => i+j+1=2. product_arr[2] 对应十位
            // i=0,j=0 => i+j+1=1. product_arr[1] 对应百位
            product_arr[i + j + 1] += digit1 * digit2;
        }
    }

    // 3. 处理进位，并转换为字符
    char* result_str = (char*)malloc(sizeof(char) * (result_len + 1));
    if (result_str == NULL) {
        free(product_arr);
        fprintf(stderr, "Memory allocation failed\n");
        exit(EXIT_FAILURE);
    }
    result_str[result_len] = '\0'; // 字符串终止符

    int carry = 0;
    for (int i = result_len - 1; i >= 0; i--) {
        int sum = product_arr[i] + carry;
        result_str[i] = (sum % 10) + '0';
        carry = sum / 10;
    }

    free(product_arr); // 释放中间整数数组

    // 4. 去除前导零
    // 找到第一个非零的字符
    int k = 0;
    while (k < result_len - 1 && result_str[k] == '0') {
        k++;
    }

    // 如果 k > 0，说明有前导零，需要截取子字符串
    if (k > 0) {
        char* final_result = (char*)malloc(sizeof(char) * (result_len - k + 1));
        if (final_result == NULL) {
            free(result_str);
            fprintf(stderr, "Memory allocation failed\n");
            exit(EXIT_FAILURE);
        }
        strcpy(final_result, result_str + k);
        free(result_str); // 释放旧的带有前导零的字符串
        return final_result;
    } else {
        return result_str; // 没有前导零，直接返回
    }
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    string multiply(string num1, string num2) {
        // 边界处理：如果任一输入是 "0"，结果就是 "0"
        if (num1 == "0" || num2 == "0") return "0";

        vector<int> A, B; // A, B 存储反向的数字，例如 "123" 变成 {3, 2, 1}
        int n = num1.size(), m = num2.size();
        for (int i = n - 1; i >= 0; i -- ) A.push_back(num1[i] - '0');
        for (int i = m - 1; i >= 0; i -- ) B.push_back(num2[i] - '0');

        vector<int> C(n + m); // C 存储乘积的中间结果，从低位到高位
        for (int i = 0; i < n; i ++ )
            for (int j = 0; j < m; j ++ )
                C[i + j] += A[i] * B[j]; // 核心乘法累加，正确使用 +=

        // 进位处理
        for (int i = 0, t = 0; i < C.size(); i ++ ) { // t 为进位
            t += C[i]; // 累加当前位的值和进位
            C[i] = t % 10; // 当前位的结果
            t /= 10; // 新的进位
        }

        // 去除前导零
        int k = C.size() - 1;
        while (k > 0 && !C[k]) k -- ; // 找到第一个非零的高位

        string res;
        while (k >= 0) res += C[k -- ] + '0'; // 将数字转换为字符并构造结果字符串

        return res;
    }
};
```
