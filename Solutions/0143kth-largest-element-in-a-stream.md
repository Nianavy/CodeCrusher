https://leetcode.cn/problems/kth-largest-element-in-a-stream

### Think
```
Priority-Queue
```

### Ways
By C++
```C++
class KthLargest {
public:
    priority_queue<int, vector<int>, greater<int>> q;
    int k;
    KthLargest(int k, vector<int>& nums) {
        this->k = k;
        for (auto &x: nums) add(x);
    }
    
    int add(int val) {
        q.push(val);
        if (q.size() > k) q.pop();
        return q.top();
    }
};
```
