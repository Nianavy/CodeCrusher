# Question

[clone-graph](https://leetcode.cn/problems/clone-graph/)



# Answer

> solution

【核心思路总结】

问题本质
复制一个无向图（实际上是有向边但双向连接），节点可能有自环和重复连接。核心是节点唯一性和关系一致性。

核心洞察

数据结构分离：

哈希表：原节点→新节点，是核心数据结构，决定"复制状态"
图复制：DFS/BFS遍历，在哈希表中查询/创建节点，再挂邻居
递归确定性：

遇到新节点：创建副本，注册哈希表，递归邻居
遇到老节点：直接返回哈希表中的副本（消除复制副作用）

复杂度

时间：O(N + E)，每个节点和边访问一次不变
空间：O(N)，哈希表和递归栈深度（最坏N）
哲学总结

先定义数据：哈希表是核心，决定一切行为
消除特殊情况：自环、重复边都通过"查缓存"统一解决
最直接路径：DFS比BFS更本质（函数栈就是图栈），代码更短
最终形态：哈希表 + 递归 = 任何图复制问题的万能钥匙。

### Way Of C

> source code

```c
TODO
```

---

### Way Of C++

> source code

```c++
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> neighbors;
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
*/
class Solution {
public:
    unordered_map<Node*, Node*> visited;
    
    Node* cloneGraph(Node* node) {
        if (!node) return nullptr;
        if (visited.count(node)) return visited[node];
        
        Node* copy = new Node(node->val);
        visited[node] = copy;
        
        for (Node* neighbor : node->neighbors) {
            copy->neighbors.push_back(cloneGraph(neighbor));
        }
        return copy;
    }
};
```
