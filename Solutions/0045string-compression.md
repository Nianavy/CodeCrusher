https://leetcode.cn/problems/string-compression

### THINK
```TXT
初始化变量：
n：字符数组的大小。
write：指向写入位置的指针，初始为0。
left：指向当前字符块开始位置的指针，初始为0。

遍历字符数组：
使用read指针遍历整个字符数组。
在每次循环中，检查当前字符是否是最后一个字符，或者当前字符与下一个字符是否不同。

处理字符块：
如果是字符块的结束（即当前字符与下一个字符不同，或者是最后一个字符），则进行以下操作：
将当前字符写入chars[write]，并将write指针向后移动。
计算当前字符块的长度num，即read - left + 1。

处理重复字符的计数：
如果num大于1，表示有重复字符：
记录当前写入位置的anchor。
使用while循环将num的每一位数字转换为字符并写入chars数组。
由于是从低位到高位写入，因此需要使用reverse函数将这些字符反转。

更新指针：
更新left指针为read + 1，准备处理下一个字符块。

返回结果：
最后返回write，即压缩后字符数组的有效长度。
```
### WAYS
BY C++:
```CPP
class Solution {
public:
    int compress(vector<char>& chars) {
        int n = chars.size();
        int write = 0, left = 0;
        for (int read = 0; read < n; read++) {
            if (read == n - 1 || chars[read] != chars[read + 1]) {
                chars[write++] = chars[read];
                int num = read - left + 1;
                if (num > 1) {
                    int anchor = write;
                    while (num > 0) {
                        chars[write++] = num % 10 + '0';
                        num /= 10;
                    }
                    reverse(&chars[anchor], &chars[write]);
                }
                left = read + 1;
            }
        }
        return write;
    }
};
```
