# Question

[linked-list-cycle](https://leetcode.cn/problems/linked-list-cycle/)



# Answer

> solution

链表环检测算法设计思路
一、核心洞察
问题本质：检测链表中是否存在环。通过指针遍历，若能重复访问同一节点，则存在环。

关键发现：在环形结构中，"快指针"相对于"慢指针"的速度差（每步2格 vs 每步1格）会使得它们在有限步数内必定相遇。

二、算法选择
方案对比
哈希表法：记录访问过的节点地址 → 时间 O(N)，空间 O(N)
修改链表法：标记已访问节点 → 时间 O(N)，空间 O(1)（但破坏数据）
快慢指针法：利用速度差检测 → 时间 O(N)，空间 O(1) ⭐最优
为什么选择快慢指针？
空间最优：常数空间复杂度
无需修改原数据：保持链表完整性
实现简单：只需两个指针变量
性能稳健：最坏情况遍历一次链表
三、设计步骤
第一步：处理边界条件
<C>
// 空链表直接返回 false
if (head == NULL) return false;
// 单节点且无环的情况由循环条件自然处理
第二步：初始化指针
<C>
struct ListNode *slow = head;  // 慢指针，每次走1步
struct ListNode *fast = head;  // 快指针，每次走2步
第三步：遍历循环
<C>
while (fast != NULL && fast->next != NULL) {
    slow = slow->next;          // 移动1步
    fast = fast->next->next;    // 移动2步
    if (slow == fast) {         // 相遇检测
        return true;
    }
}
第四步：返回结果
<C>
return false;  // 未相遇，无环
四、复杂度分析
指标	计算过程	结果
时间	最多遍历链表长度 L	O(N)
空间	仅使用2个指针变量	O(1)
最坏情况	无环时快指针达到链表末尾	偏差 O(N)
相遇点	取决于环长和非环长度	必定存在
五、简化流程图
<TEXT>
开始
  ↓
检查 head 为空 → 返回 false
  ↓
初始化 slow = fast = head
  ↓
进入循环
  │
  ├─ 移动 slow (1步)
  ├─ 移动 fast (2步)
  ├─ 相遇？ → 是 → 返回 true
  │            │
  └─ 否 → 继续循环直至 fast 为 NULL
  ↓
退出循环 → 返回 false
六、实现要点
指针移动顺序：先移动后检查（但当前代码是先检查边界再移动），更安全的方式是：

<C>
while (fast && fast->next) {
    slow = slow->next;
    fast = fast->next->next;
    if (slow == fast) return true;
}

### Way Of C

> source code

```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
bool hasCycle(struct ListNode *head) {
    if (!head) return false; // 处理空输入
    struct ListNode *slow = head;
    struct ListNode *fast = head;
    while (fast != NULL && fast->next != NULL) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) {
            return true;
        }
    }
    return false;
}
```

---

### Way Of C++

> source code

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if (!head) return false;  // 显式处理空指针
    
        ListNode *slow = head;
        ListNode *fast = head;
        
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) return true;
        }
        return false;
    }
};
```
