https://leetcode.cn/problems/implement-queue-using-stacks

### Think
```TXT
使用两个栈 inStack 和 outStack，分别负责入队和出队操作。下面我们逐步分析各个部分的思路。

1. 数据成员
cpp
stack<int> inStack, outStack;
inStack：用于存储新入队的元素。
outStack：用于辅助实现出队操作，模拟队列的先进先出特性。

2. in2out 函数
cpp
void in2out() {
    while (!inStack.empty()) {
        outStack.push(inStack.top());
        inStack.pop();
    }
}
作用：将 inStack 中的所有元素转移到 outStack 中。
为什么需要这个函数？
当需要出队或查看队首元素时，队列要求先进先出。而栈是后进先出，因此需要将 inStack 中的元素倒序放入 outStack，这样 outStack 的栈顶元素就是队列的队首元素。

3. push 函数
cpp
void push(int x) {
    inStack.push(x);
}
作用：将元素 x 压入 inStack，即入队操作。
思路：由于 inStack 只负责存储新入队的元素，因此直接把元素压入 inStack 即可。

4. pop 函数
cpp
int pop() {
    if (outStack.empty()) {
        in2out();
    }
    int x = outStack.top();
    outStack.pop();
    return x;
}
作用：移除队列的队首元素并返回它。
思路：
如果 outStack 为空，调用 in2out() 将 inStack 中的元素转移过来。
从 outStack 中弹出栈顶元素，该元素就是队列的队首元素。

5. peek 函数
cpp
int peek() {
    if (outStack.empty()) {
        in2out();
    }
    return outStack.top();
}
作用：返回队列的队首元素，但不移除它。
思路：
如果 outStack 为空，调用 in2out() 将 inStack 中的元素转移过来。
返回 outStack 的栈顶元素，该元素就是队列的队首元素。

6. empty 函数
cpp
bool empty() {
    return inStack.empty() && outStack.empty();
}
作用：判断队列是否为空。
思路：只有当 inStack 和 outStack 都为空时，队列才是空的。
```

### Ways
By C++:
```CPP
class MyQueue {
private:
    stack<int> inStack, outStack;
    void in2out() {
        while (!inStack.empty()) {
            outStack.push(inStack.top());
            inStack.pop();
        }
    }
public:
    MyQueue() { }
    
    void push(int x) {inStack.push(x);}
    
    int pop() {
        if (outStack.empty()) {in2out();}
        int x = outStack.top();
        outStack.pop();
        return x;
    }
    
    int peek() {
        if (outStack.empty()) {in2out();}
        return outStack.top();
    }
    
    bool empty() { return inStack.empty() && outStack.empty();  }
};
```
