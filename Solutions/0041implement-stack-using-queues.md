https://leetcode.cn/problems/implement-stack-using-queues

### Think
```TXT
构造函数MyStack()：

构造函数中没有额外的操作，只初始化了两个空队列。
入栈操作push(int x)：

首先将元素x入队到queue2中。
然后，它会将queue1中的所有元素弹出并入队到queue2，这样queue2就包含了所有元素，且顺序与queue1相反。
最后，交换queue1和queue2，这样queue1就拥有了所有元素，且顺序与原来的queue2相同，实现了入栈操作。
出栈操作pop()：

直接从queue1中弹出队首元素，这相当于从栈顶弹出元素。
查看栈顶元素top()：

返回queue1队首元素，即栈顶元素。
判断空栈empty()：

检查queue1是否为空。如果queue1为空，则表示栈为空。
```

### Ways
By C++:
```CPP
class MyStack {
public:
    queue<int> queue1;
    queue<int> queue2;

    MyStack() {}
    
    void push(int x) {
        queue2.push(x);
        while (!queue1.empty()) {
            queue2.push(queue1.front());
            queue1.pop();
        }   
        swap(queue1, queue2);
    }
    
    int pop() {
        int r = queue1.front();
        queue1.pop();
        return r;
    }
    
    int top() {
        int r = queue1.front();
        return r;
    }
    
    bool empty() {return queue1.empty();}
};
```
