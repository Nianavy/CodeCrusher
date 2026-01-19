# Question

[min-stack](https://leetcode.cn/problems/min-stack/)



# Answer

> solution

## **核心策略：维护一个“最小值历史”**

不要只记录“谁是最小值”，而是**记录下每一个时刻的“当前最小值是什么”**。

这是解决问题的根本。`getMin()` 不再需要搜索，它只是直接读取你已经安排好的值。

## **两种实现方式（等价的思想）**

### **方式一：栈式解法（C++/通用思路）**

1.  **主栈 `s`**：存放所有元素。
2.  **最小值栈 `ms`**：**同步栈**。每当 `s.push(x)` 时：
    *   如果 `ms` 为空 或 `x <= ms.top()`，则 `ms.push(x)`。
    *   否则，`ms` 保持不变。
3.  **核心逻辑**：`ms` 存放的是**非递减序列**，它就是 `min_history` 的一种形式。
4.  **POP 操作**：如果 `s.top() == ms.top()`，则 `ms.pop()`。这完美地“回退”了最小值的历史。
5.  **时间复杂度**：**全 O(1)**。没有意外，没有退化。

### **方式二：数组式解法（C 语言直接实现）**

1.  **数据结构**：
    *   `data[]`：存放所有值。
    *   **`min_history[]`**：并行的数组，长度与 `data` 相同。`min_history[i]` 始终记录 `data[0..i]` 中的最小值。
2.  **PUSH 操作**：
    *   写入 `data`。
    *   计算并写入 `min_history`：`min_history[size] = min(val, min_history[size-1])`。（`size=0` 时特殊处理）。
3.  **POP 操作**：**仅递减计数器 `size`**。`min_history` 中的历史值会自然“回退”，`getMin()` 直接从 `min_history[size-1]` 获取。
4.  **时间复杂度**：**全 O(1)**。

## **关键要点**

1.  **“历史”是答案**：`pop` 时能瞬间得到正确的最小值，是因为你提前存储了正确的历史信息。
2.  **空间换时间**：`min_history` 或最小值栈 `ms` 是明确的空间开销，换取了完美的 O(1) 时间性能。**这是值得的**。
3.  **C 语言实现的陷阱**：一个完整的实现必须处理 `realloc`，而栈式解法（如 C++ `std::stack`）自动处理了这一点。
4.  **“好品味”的体现**：两种方案都没有复杂的条件分支，在冗余数据（`min_history`/`ms`）的处理上清晰、一致、无特殊情况。

**总结：用明确的、并行的数据结构来记录最小值的变化历史，从而在 O(1) 时间内实现 `getMin`。** 这是解决此问题最直接、最没有歧义的方式。

### Way Of C

> source code

```c
typedef struct {
    int* data;          // 原始数据
    int* min_history;   // 并行的历史最小值记录
    int size;           // 当前元素数量
    int capacity;       // 分配的容量
} MinStack;
MinStack* minStackCreate() {
    MinStack* obj = (MinStack*)malloc(sizeof(MinStack));
    if (!obj) return NULL;
    
    // 初始分配一个小的容量
    int init_capacity = 4;
    obj->data = (int*)malloc(init_capacity * sizeof(int));
    obj->min_history = (int*)malloc(init_capacity * sizeof(int));
    
    if (!obj->data || !obj->min_history) {
        free(obj->data);
        free(obj->min_history);
        free(obj);
        return NULL;
    }
    
    obj->size = 0;
    obj->capacity = init_capacity;
    return obj;
}
// 内部扩容函数：处理 AddressSanitizer 错误的根本
static void resize_if_needed(MinStack* obj) {
    if (obj->size < obj->capacity) return; // 不需要扩容
    double new_capacity_double = obj->capacity * 1.5; // 增长因子 1.5
    int new_capacity = (int)new_capacity_double;
    // 防止溢出
    if (new_capacity <= obj->capacity) {
        new_capacity = obj->capacity * 2;
    }
    int* new_data = (int*)realloc(obj->data, new_capacity * sizeof(int));
    int* new_min_history = (int*)realloc(obj->min_history, new_capacity * sizeof(int));
    if (!new_data || !new_min_history) {
        // realloc 失败，处理方式取决于需求。这里我们选择不扩容，让 push 失败。
        // 更严格的做法是抛出错误或让程序崩溃。
        // 为简单起见，我们让程序无法动态增长。
        // 在实际生产中，这里应该有更健壮的错误处理。
        free(new_data);
        free(new_min_history);
        return;
    }
    obj->data = new_data;
    obj->min_history = new_min_history;
    obj->capacity = new_capacity;
}
void minStackPush(MinStack* obj, int val) {
    if (!obj) return;
    // 关键：在写入前确保内存足够！这是解决你 AddressSanitizer 错误的步骤。
    resize_if_needed(obj);
    // 如果扩容后仍然不够（罕见），安全退出。
    if (obj->size >= obj->capacity) return;
    // 记录数据
    obj->data[obj->size] = val;
    // 记录当前最小值历史：比较 val 和上一个最小值（size-1处），取较小者
    if (obj->size == 0) {
        obj->min_history[0] = val; // 第一个元素，最小值就是它自己
    } else {
        int prev_min = obj->min_history[obj->size - 1];
        obj->min_history[obj->size] = (val < prev_min) ? val : prev_min;
    }
    obj->size++; // 最后才增加 size
}
void minStackPop(MinStack* obj) {
    if (!obj || obj->size == 0) return; // 栈空，无需操作
    obj->size--; // 仅仅缩减 size，min_history 里的历史值会自动“回退”
}
int minStackTop(MinStack* obj) {
    if (!obj || obj->size == 0) {
        // 根据题目要求，调用者应保证非空。这里返回未定义值作为错误指示。
        return INT_MIN;
    }
    return obj->data[obj->size - 1];
}
int minStackGetMin(MinStack* obj) {
    if (!obj || obj->size == 0) {
        return INT_MIN; // 同上
    }
    // 直接返回当前大小对应的历史最小值
    return obj->min_history[obj->size - 1];
}
void minStackFree(MinStack* obj) {
    if (obj) {
        free(obj->data);
        free(obj->min_history);
        free(obj);
    }
}

/**
 * Your MinStack struct will be instantiated and called as such:
 * MinStack* obj = minStackCreate();
 * minStackPush(obj, val);
 
 * minStackPop(obj);
 
 * int param_3 = minStackTop(obj);
 
 * int param_4 = minStackGetMin(obj);
 
 * minStackFree(obj);
*/
```

---

### Way Of C++

> source code

```c++
class MinStack {
private:
    std::stack<int> main_stack;
    std::stack<int> min_stack; // 只记录最小值
public:
    MinStack() {
        // 无需特殊初始化
    }
    void push(int val) {
        main_stack.push(val);
        // 如果辅助栈为空，或者当前值小于等于辅助栈的最小值，就压入
        if (min_stack.empty() || val <= min_stack.top()) {
            min_stack.push(val);
        }
    }
    void pop() {
        // 如果主栈顶元素等于辅助栈顶元素（也就是当前的最小值），那么辅助栈也要弹出
        if (main_stack.top() == min_stack.top()) {
            min_stack.pop();
        }
        main_stack.pop();
    }
    int top() {
        return main_stack.top();
    }
    int getMin() {
        return min_stack.top();
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(val);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```
