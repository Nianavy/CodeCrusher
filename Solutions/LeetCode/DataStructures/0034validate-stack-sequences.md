https://leetcode.cn/problems/validate-stack-sequences

### Think
```txt
栈的基本操作：

栈是一种后进先出（LIFO）的数据结构，支持入栈（push）和出栈（pop）操作。
双指针法：

使用两个指针 i 和 j，分别遍历 pushed 和 popped 数组。
i 用于遍历 pushed 数组，j 用于遍历 popped 数组。
入栈操作：

在循环中，首先将 pushed[i] 入栈。
出栈操作：

每次入栈后，检查栈顶元素是否与 popped[j] 相等。
如果相等，则进行出栈操作，并将 j 指针向前移动，表示已经成功出栈一个元素。
这个过程会持续进行，直到栈为空或栈顶元素不再与 popped[j] 相等。
结束条件：

当所有元素都被处理完后，检查栈是否为空。如果栈为空，说明所有元素都能按照 popped 的顺序出栈，返回 true；否则返回 false。
```

### Ways
By C++:
```cpp
class Solution {
public:
    bool validateStackSequences(vector<int>& pushed, vector<int>& popped) {
        stack<int> st;
        int n = pushed.size();
        for (int i=0, j=0; i<n; ++i) {
            st.emplace(pushed[i]);
            while (!st.empty() && st.top() == popped[j]) {
                st.pop();
                ++j;
            }
        }
        return st.empty();
    }
};
```
