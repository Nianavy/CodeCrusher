https://leetcode.cn/problems/reverse-integer

### think:

采用循环检查输入值：

在循环内部，首先检查反转后的数字`ans`是否会超出`int`类型的范围。`INT_MIN`和`INT_MAX`是定义在`<climits>`头文件中的宏，分别代表`int`类型能表示的最小值和最大值。如果`ans`乘以10后加上下一个数字会超出这个范围，那么函数返回0。

然后取出输入值的最后一位，并更新输入值变量（去掉刚刚取出的数），随后将ans*10再加上取出的最后一位数。达到实现reverse integer的效果

### ways:

By C++:

```cpp
#include <climits>

class Solution {
public:
    int reverse(int x) {
        int ans = 0;
        while (x) {
            if (ans < INT_MIN / 10 || ans > INT_MAX / 10)
                return 0;
            int integer = x % 10;
            x /= 10;
            ans = ans * 10 + integer;
        }
        return ans;
    }
};

```

