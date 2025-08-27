# Question

[longest substring without repeating characters](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)



# Answer

### Way Of C

> source code

```c
int lengthOfLongestSubstring(char* s) {
    int count[256] = {0};
    int len = 0;
    int i, j;
    for (i = 0, j = 0; s[i] != '\0'; ++i) {
        count[s[i]]++;
        while (count[s[i]] > 1) {
            len = i - j > len ? i - j : len;
            count[s[j++]] -= 1;
        }
    }
    return i - j > len ? i - j : len;
}
```

---


### Way Of C++

> source code

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        vector<int> count(256);
        int len = 0;
        int i, j;
        for (i = 0, j = 0; i < s.length(); ++i) {
            count[s[i]]++;
            while (count[s[i]] > 1) {
                len = i - j > len ? i - j : len;
                count[s[j++]] -= 1;
            }
        }
        return i - j > len ? i - j : len;
    }
};
```

> think

1.  **滑动窗口 (Sliding Window) 算法的核心运用：**
    *   **思想：** 这是解决字符串或数组子串/子序列问题的一种高效模式。它维护一个“窗口”，通过两个指针（通常是 `i` 和 `j`）来扩展和收缩这个窗口。
    *   **体现：**
        *   `i` 指针（外层 `for` 循环）负责**扩展窗口**：每次循环将 `s[i]` 加入当前窗口。
        *   `j` 指针（内层 `while` 循环）负责**收缩窗口**：当窗口条件不再满足（即出现重复字符）时，`j` 指针开始移动，将 `s[j]` 从窗口中移除，直到窗口条件重新满足。
    *   **效率：** 这种模式避免了暴力解法中大量的重复计算。每个字符在字符串中最多被 `i` 指针访问一次，被 `j` 指针访问一次，因此时间复杂度是线性的 O(N)，其中 N 是字符串的长度。

2.  **频率计数数组 (Frequency Counter Array) 的高效利用：**
    *   **思想：** `count[256]` (或 `std::vector<int> count(256)`) 充当了一个字符频率映射表。它利用了 ASCII 字符集大小固定的特性。
    *   **体现：**
        *   `count[s[i]]++`：当 `i` 指针扩展窗口时，快速增加新加入字符的计数。
        *   `while (count[s[i]] > 1)`：这是判断窗口内是否有重复字符的核心条件。一旦发现 `s[i]` 的计数大于 1，就意味着 `s[i]` 在当前窗口内重复出现了。
        *   `count[s[j++]] -= 1`：当 `j` 指针收缩窗口时，快速减少被移除字符的计数，直到重复字符被完全移除。
    *   **效率：** 由于 `count` 数组的大小是固定的 256，所有对字符计数的增、减、查操作都可以在 O(1) 的常数时间内完成。这使得整个算法的常数因子非常小，进一步提升了实际性能，同时带来了 O(1) 的空间复杂度。

3.  **动态维护最长长度 `len`：**
    *   **思想：** `len` 变量用于记录到目前为止遇到的最长无重复子串的长度。
    *   **体现：**
        *   `len = i - j > len ? i - j : len;` (C++ 中用 `std::max(len, i - j)`)：这个语句在 `while` 循环内部（当 `j` 移动之前）以及 `for` 循环结束后被调用。它计算当前无重复子串的长度 `(i - j)`，并更新 `len`。
        *   **关键点：** `len` 的更新时机至关重要。它应该在窗口收缩之前（即 `j` 移动之前）记录当前有效的窗口长度。最后一次更新在 `for` 循环结束后，确保即使最后一个子串是无重复的且是最大的，也能被正确捕获。

4.  **无副作用设计：**
    *   **思想：** 函数只读取输入字符串 `s`，不对其进行任何修改。
    *   **体现：** 这使得函数成为一个“纯函数”，易于理解、测试和重用，符合良好的软件工程实践。

**总结：**
这两个实现的关键在于它们完美地结合了**滑动窗口算法**的效率和**频率计数数组**的常数时间查找/更新能力。通过双指针 `i` 和 `j` 的巧妙协作，以及 `count` 数组的辅助，算法能够以线性的时间复杂度和常数的空间复杂度，准确地找出给定字符串中最长的无重复字符子串。这是一个教科书般的算法实现，简洁、高效且健壮。
