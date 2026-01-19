# Question

[compare-version-numbers](https://leetcode.cn/problems/compare-version-numbers/)



# Answer

> solution

**解决思路总结：**

1.  **数据结构与遍历**：  
    使用两个指针（C）或迭代器位置（C++）同时遍历两个字符串，逐段解析数字字符，无需创建子字符串或额外数据结构。

2.  **零分支解析**：  
    跳过对前导零的特殊处理（自然解析中自动忽略），也不用条件判断来处理空段——数字解析循环只在遇到点时停止，空段会被直接跳过（数值保持为0）。

3.  **逐段比较**：  
    每次解析完一段，立即比较数值。如果段值不等，直接返回1或-1；相等则继续解析下一段。

4.  **边界处理**：  
    - 字符串结束时，剩余段视为数值0。  
    - 连续点或结尾点会生成空段，自然被静默处理为0。  
    - 循环终止条件：两个字符串都遍历完毕。

### Way Of C

> source code

```c
int compareVersion(char *v1, char *v2) {
    char *p1 = v1, *p2 = v2;
    while (*p1 || *p2) {
        int num1 = 0, num2 = 0;
        // 解析 v1 的下一段
        while (*p1 && *p1 != '.') {
            num1 = num1 * 10 + (*p1 - '0');
            p1++;
        }
        if (*p1) p1++; // 跳过点（如果有）
        // 解析 v2 的下一段
        while (*p2 && *p2 != '.') {
            num2 = num2 * 10 + (*p2 - '0');
            p2++;
        }
        if (*p2) p2++; // 跳过点（如果有）
        // 比较
        if (num1 != num2) {
            return num1 > num2 ? 1 : -1;
        }
    }
    return 0;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int compareVersion(std::string v1, std::string v2) {
        size_t i = 0, j = 0;
        while (i < v1.size() || j < v2.size()) {
            int num1 = 0, num2 = 0;
            // 解析 v1 的下一段
            while (i < v1.size() && v1[i] != '.') {
                num1 = num1 * 10 + (v1[i] - '0');
                i++;
            }
            if (i < v1.size()) i++; // 跳过点
            // 解析 v2 的下一段
            while (j < v2.size() && v2[j] != '.') {
                num2 = num2 * 10 + (v2[j] - '0');
                j++;
            }
            if (j < v2.size()) j++; // 跳过点
            // 比较
            if (num1 != num2) {
                return num1 > num2 ? 1 : -1;
            }
        }
        return 0;
    }
};
```
