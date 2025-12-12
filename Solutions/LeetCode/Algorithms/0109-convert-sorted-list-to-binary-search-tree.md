# Question

[convert-sorted-list-to-binary-search-tree](https://leetcode.cn/problems/convert-sorted-list-to-binary-search-tree/)



# Answer

> solution

## 有序链表到平衡 BST (高效 $O(N)$ 构造法)

### 1. 核心挑战：链表的随机访问低效

有序数组的转换是 $O(N)$ 的，因为它可以在 $O(1)$ 时间内找到任何中间元素。但链表做不到 $O(1)$ 随机访问。

*   **低效方案**：如果使用传统的**自上而下的分治**（如快慢指针或重复遍历），虽然能找到中间点，但每次都需要从头开始遍历，导致总时间复杂度退化为 $O(N \log N)$。
*   **高效方案**：必须设计一种方法，在**单次 $O(N)$ 遍历**中完成树的构建。

### 2. 算法核心：中序遍历构造 (自下而上)

为了实现 $O(N)$ 效率，我们利用链表本身就是 BST 的**中序遍历**序列这一事实。我们不是自上而下地寻找根节点并分割链表，而是**自下而上地构造树**，并让链表指针同步移动。

#### A. 预处理：确定虚拟边界

首先，遍历链表以获取其总长度 $N$。这个长度 $N$ 定义了我们虚拟的中序序列的范围 `[0, N-1]`。

#### B. 递归同步 (DFS)

递归函数 `dfs(head_ptr, lo, hi)` 负责构建对应于中序序列 `[lo, hi]` 范围的子树。

1.  **左子树 (Left)**：递归调用 `dfs` 构建左子树。这一步会递归深入到最左侧的叶节点。
2.  **根节点赋值 (Root)**：当左子树完全构建完毕并返回时，此时，**全局的链表头指针 (`*head_ptr`) 正好指向当前子树在中序遍历序列中的值。**
3.  **推进链表**：将 `(*head_ptr)` 的值赋给当前 BST 根节点，然后将链表头指针推进一个位置 (`*head_ptr = (*head_ptr)->next`)。
4.  **右子树 (Right)**：递归调用 `dfs` 构建右子树。

通过**传递链表头的地址（双重指针或引用）**，我们在递归的过程中保持了链表指针的全局状态，完美地模拟了中序遍历的序列，从而实现了 $O(N)$ 的单次遍历构建。

### 3. C/C++ 实现的额外要求

*   **C++ 风格**：使用引用 `ListNode*& head` 传递链表指针，实现中序遍历的全局同步。代码简洁，内存管理由 `new` 负责。
*   **C 风格（实用主义）**：使用双重指针 `struct ListNode **head_ptr` 来实现指针的修改。**最重要且合规的要求是：** 必须为所有 `malloc` 的 `TreeNode` 提供一个配套的 `freeTree` 函数，以避免内存泄漏，体现 C 语言对内存契约的绝对遵守。

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
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */
// 递归中序构造：需要传递链表头部的双重指针 (struct ListNode **)
static struct TreeNode *dfs_inorder(struct ListNode **head_ptr, int lo, int hi)
{
    if (lo > hi) {
        return NULL;
    }

    // 1. 递归构建左子树 (Left)
    int mid = lo + (hi - lo) / 2; 
    struct TreeNode *node = malloc(sizeof(*node));
    if (!node) exit(EXIT_FAILURE);

    // 递归调用：先构建左侧 (消耗掉 lo 到 mid-1 的链表节点)
    node->left = dfs_inorder(head_ptr, lo, mid - 1);
    
    // 2. 赋值当前根节点 (Root)
    // 链表当前指向的节点就是当前 BST 的根节点
    node->val = (*head_ptr)->val;
    (*head_ptr) = (*head_ptr)->next; // 推进链表指针，使其指向右子树的第一个节点
    
    // 3. 递归构建右子树 (Right)
    node->right = dfs_inorder(head_ptr, mid + 1, hi);
    
    return node;
}

// 外部接口函数
struct TreeNode* sortedListToBST(struct ListNode* head)
{
    if (head == NULL) {
        return NULL;
    }
    
    // 1. O(N) 遍历获取长度
    struct ListNode *p;
    int len = 0;
    for (p = head; p != NULL; p = p->next) {
        len++;
    }
    
    // 2. 传递头指针的地址，启动中序构造
    return dfs_inorder(&head, 0, len - 1);
}

// --- 内存清理函数 ---

// 清理构造出的 BST 节点
static void freeTree(struct TreeNode* root) {
    if (root == NULL) return;
    freeTree(root->left);
    freeTree(root->right);
    free(root);
}

// 清理输入的链表节点 (如果在 main 函数中是动态创建的)
static void freeList(struct ListNode* head) {
    struct ListNode* current = head;
    struct ListNode* next;
    while (current != NULL) {
        next = current->next;
        free(current);
        current = next;
    }
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
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
private:
    // **核心：通过引用传递链表头，实现中序遍历的同步**
    TreeNode* build_bst_inorder(ListNode*& head, int start_index, int end_index) {
        if (start_index > end_index) {
            return nullptr;
        }

        // 1. 递归构建左子树 (Left)
        // 中间点依然是 mid，用于划分虚拟中序序列
        int mid = start_index + (end_index - start_index) / 2; 
        TreeNode *left_child = build_bst_inorder(head, start_index, mid - 1);

        // 2. 根节点赋值 (Root)
        // 链表当前指向的节点就是当前子树的根节点（中序遍历的顺序）
        TreeNode *root = new TreeNode(head->val);
        root->left = left_child;
        
        // 推进链表指针
        head = head->next; 

        // 3. 递归构建右子树 (Right)
        root->right = build_bst_inorder(head, mid + 1, end_index);
        
        return root;
    }

public:
    TreeNode* sortedListToBST(ListNode* head) {
        if (!head) return nullptr;

        // 1. O(N) 遍历，获取长度
        int len = 0;
        for (ListNode* p = head; p; p = p->next) {
            len++;
        }
        
        // 2. 传递 head 的引用，进行中序构造
        return build_bst_inorder(head, 0, len - 1);
    }
};
```
