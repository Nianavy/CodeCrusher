https://leetcode.cn/problems/design-a-stack-with-increment-operation


### Think
```txt
CustomStack(int maxSize) 构造函数:

初始化一个大小为 maxSize 的 vector<int> 数组 stk 用于存储栈元素。
初始化 top = -1 表示栈为空。
push(int x) 入栈操作:

检查栈是否已满 (top != stk.size() - 1)。
如果栈未满，则将 top 加 1，并将 x 放入 stk[top] 位置。
pop() 出栈操作:

检查栈是否为空 (top == -1)。
如果栈为空，返回 -1。
如果栈非空，先将 top 减 1，然后返回 stk[top + 1]（即原来的栈顶元素）。
increment(int k, int val)  增量操作:

计算要增加的元素个数 lim，取 k 和栈中实际元素个数 top + 1 的较小值。
从栈底开始，循环 lim 次，将每个元素的值增加 val。
```

### Ways
By C++:
```cpp
class CustomStack {
public:
    vector<int> stk;
    int top;
    CustomStack(int maxSize) {
        stk.resize(maxSize);
        top = -1;
    }
    
    void push(int x) {
        if (top != stk.size() - 1) {
            ++top;
            stk[top] = x;
        }
    }
    
    int pop() {
        if (top == -1) return -1;
        --top;
        return stk[top+1];
    }
    
    void increment(int k, int val) {
        int lim = min(k, top+1);
        for (int i=0; i<lim; ++i) stk[i] += val;
    }
};
```
