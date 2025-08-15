https://leetcode.cn/problems/design-circular-queue

### Think
```txt
容量处理:  capacity = k + 1  队列的实际容量比用户指定的 k 大 1。这是为了区分队列满和队列空的情况。如果 rear == front，队列为空；如果 (rear + 1) % capacity == front，队列满。这种设计牺牲了一个存储空间，简化了判断逻辑，避免了复杂的边界处理。

循环利用空间: 使用 (rear + 1) % capacity 和 (front + 1) % capacity  来实现循环队列的核心逻辑。% 运算符（取模运算）保证了 front 和 rear 指针在 0 到 capacity - 1 的范围内循环移动，从而实现了空间的循环利用。  就像一个环形的跑道，跑完一圈后又回到了起点。

enQueue() (入队操作):

首先检查队列是否已满 (isFull()).
如果未满，将 value 插入到 rear 指针指向的位置。
然后将 rear 指针后移一位，使用取模运算实现循环。
deQueue() (出队操作):

首先检查队列是否为空 (isEmpty()).
如果不为空，将 front 指针后移一位，使用取模运算实现循环。 注意，这里并没有真正删除元素，只是移动了 front 指针，相当于逻辑上删除了队首元素。
Front() (获取队首元素):

首先检查队列是否为空。
如果不为空，返回 front 指针指向的元素。
Rear() (获取队尾元素):

首先检查队列是否为空。
如果不为空，返回 (rear - 1 + capacity) % capacity 指向的元素。这里需要注意 rear - 1 可能为负数，所以加上 capacity 再取模，保证结果的正确性。
isEmpty() (判断队列是否为空):  如果 rear == front，则队列为空。

isFull() (判断队列是否已满):  如果 (rear + 1) % capacity == front，则队列已满。
```

### Ways
By C++:
```cpp
class MyCircularQueue {
private:
    int front;
    int rear;
    int capacity;
    vector<int> elements;
public:
    MyCircularQueue(int k) {
        this->capacity = k + 1;
        this->elements = vector<int>(capacity);
        rear = front = 0;
    }
    
    bool enQueue(int value) {
        if (isFull()) { return false;}
        elements[rear] = value;
        rear = (rear+1)%capacity;
        return true;
    }
    
    bool deQueue() {
        if (isEmpty()) {return false;}
        front = (front+1)%capacity;
        return true;
    }
    
    int Front() {
        if (isEmpty()) {return -1;}
        return elements[front];
    }
    
    int Rear() {
        if (isEmpty()) {return -1;}
        return elements[(rear-1+capacity) % capacity];
    }
    
    bool isEmpty() {
        return rear == front;
    }
    
    bool isFull() {
        return ((rear + 1)%capacity) == front;
    }
};
```
