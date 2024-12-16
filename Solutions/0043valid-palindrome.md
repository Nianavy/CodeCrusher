https://leetcode.cn/problems/valid-palindrome

### Think
```TXT
输入处理：
使用 string sgood; 创建一个新的字符串 sgood，用于存储处理后的字符。
遍历输入字符串 s 中的每个字符 ch，通过 isalnum(ch) 检查字符是否是字母或数字。
如果是，则将其转换为小写并添加到 sgood 中，使用 tolower(ch)。

初始化指针：
int n = sgood.size(); 获取处理后字符串的长度。
使用两个指针 left 和 right，分别指向字符串的开始和结束位置。

双指针比较：
进入 while (left < right) 循环，比较 sgood[left] 和 sgood[right]。
如果两个字符不相等，返回 false，表示不是回文。
如果相等，移动指针：++left; 和 --right;，继续比较下一个字符。

返回结果：
如果所有字符都匹配，循环结束后返回 true，表示字符串是回文。
```

### Ways
By C++:
```CPP
class Solution {
public:
    bool isPalindrome(string s) {
        string sgood;
        for (char ch: s) {
            if (isalnum(ch)) {sgood += tolower(ch);}
        }
        int n = sgood.size();
        int left = 0, right = n - 1;
        while (left < right) {
            if (sgood[left] != sgood[right]) {return false;}
            ++left;
            --right;
        }
        return true;
    }
};
```
