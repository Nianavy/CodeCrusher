# Question

[palindrome-number](https://leetcode.cn/problems/palindrome-number/)



# Answer

> solution

1.  **处理边缘情况和无效输入。**
    *   **负数：** 任何负数都不可能是回文数。直接排除。
    *   **零：** `0` 是回文数。
    *   **以 `0` 结尾的非零数：** 除了 `0` 本身，任何以 `0` 结尾的数字（例如 `10`, `120`）反转后会以 `0` 开头（`01`, `021`），这通常不是原始数字，所以不可能回文。这也是一个快速排除的优化点。

2.  **构建反转数。**
    *   保存原始数字的副本，因为我们会在操作中修改原数字。
    *   使用循环（`while (x != 0)`），每次迭代：
        *   取出当前数字的**最低位**（通过 `x % 10` ）。
        *   将这个最低位添加到构建中的**反转数**的末尾（通过 `reversed_x = reversed_x * 10 + digit` ）。
        *   移除当前数字的最低位（通过 `x /= 10` ）。
    *   **核心要点：** 反转数 (`reversed_x`) 必须使用一个足够大的类型（例如 `long long`），以防止当原数字 `x` 很大时，其反转值超出普通 `int` 的范围，导致溢出。

3.  **比较反转数与原始数。**
    *   循环结束后，`reversed_x` 包含了原始数字的反转形式，`x` 已经变为 `0`。
    *   将 `reversed_x` 与之前保存的**原始数字副本**进行比较。如果相等，则是回文数；否则不是。

**总结就是：把数字拆了，倒着再拼起来，然后看看是不是一样。**

### Way Of C

> source code

```c
bool isPalindrome(int x) {
    if (x < 0) return false;
    if (x == 0) return true;
    if (x % 10 == 0) return false;
    int original_x = x;
    long long reversed_x = 0;
    while (x != 0) {
        int digit = x % 10;
        reversed_x = reversed_x * 10 + digit;
        x /= 10; 
    }
    return (reversed_x == original_x);
}
```

---


### Way Of C++

> source code

```c++
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0) return 0;
        int y = x;
        long long res = 0;
        while (x) {
            res = res * 10 + x % 10;
            x /= 10;
        }
        return res == y;
    }
};
```
