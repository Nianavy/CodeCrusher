https://leetcode.cn/problems/build-an-array-with-stack-operations

### Think
```txt
初始化：

创建一个字符串向量 res 用于存储操作序列。
prev 变量用于跟踪上一个被推入的数字，初始值为 0。
遍历目标数组：

使用范围 for 循环遍历 target 中的每个数字 number。
处理未推入的数字：

在每次处理 number 之前，检查从 prev + 1 到 number - 1 的所有数字。
对于每个未推入的数字，执行一次“Push”操作，然后执行一次“Pop”操作。这是因为这些数字并不在目标数组中，所以需要先推入再弹出。
推入目标数字：

一旦到达目标数字 number，执行一次“Push”操作，将其推入结果中。
更新 prev 为当前的 number，以便在下次迭代时使用。
返回结果：

最后，返回操作序列 res。
```

### Ways
By C++:
```cpp
class Solution {
public:
    vector<string> buildArray(vector<int>& target, int n) {
        vector<string> res;
        int prev = 0;
        for (int number : target) {
            for (int i=0; i<number - prev -1; ++i) {
                res.emplace_back("Push");
                res.emplace_back("Pop");
            }
            res.emplace_back("Push");
            prev = number;
        }
        return res;
    }
};
```
