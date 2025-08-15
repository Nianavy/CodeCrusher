https://leetcode.cn/problems/fibonacci-number

### Think
```TXT
函数定义：
int fib(int n) {
这是一个名为 fib 的函数，接受一个整数 n 作为参数，返回第 n 个斐波那契数。

边界条件：
if (n < 2) return n;
这里处理了边界情况：当 n 小于 2 时，直接返回 n。因为斐波那契数列的定义是：
F(0) = 0
F(1) = 1

变量初始化：
int p = 0, q = 0, r = 1;
p 用于存储前一个斐波那契数（F(n-2)）
q 用于存储当前斐波那契数（F(n-1)）
r 用于存储下一个斐波那契数（F(n)）

循环计算：
for (int i = 2; i <= n; ++i) {
    p = q;
    q = r;
    r = p + q;
}
从 2 到 n 的循环中，依次更新 p、q 和 r：
p 更新为 q（即 F(n-2)）
q 更新为 r（即 F(n-1)）
r 更新为 p + q（即 F(n)）

返回结果：
return r;

最后返回 r，即第 n 个斐波那契数。
```

### Ways
By C++:
```C++
class Solution {
public:
    int fib(int n) {
        if (n < 2) return n;
        int p = 0, q = 0, r = 1;
        for (int i = 2; i <= n; ++i) {
            p = q;
            q = r;
            r = p + q;
        }
        return r;
    }
};
```
