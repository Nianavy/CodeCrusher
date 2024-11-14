https://leetcode.cn/problems/design-linked-list

### Think:
```txt
关键是理解链表的存储结构
```


### Ways:
By C++:
```cpp
class MyLinkedList {
int size;
ListNode *head;
public:
    MyLinkedList() {
        this->size = 0;
        this->head = new ListNode(0);
    }
    
    int get(int index) {
        if (index < 0 || index >=size) return -1;
        ListNode *cur = head;
        for (int i=0; i<=index; ++i) cur = cur->next;
        return cur->val;
    }
    
    void addAtHead(int val) {
        addAtIndex(0, val);
    }
    
    void addAtTail(int val) {
        addAtIndex(size, val);
    }
    
    void addAtIndex(int index, int val) {
        if (index > size) return;
        index = max(0, index);
        ++size;
        ListNode *pred = head;
        for (int i=0; i<index; ++i) pred = pred->next;
        ListNode *toAdd = new ListNode(val);
        toAdd->next = pred->next;
        pred->next = toAdd;
    }
    
    void deleteAtIndex(int index) {
        if (index < 0 || index >= size) return;
        --size;
        ListNode *pred = head;
        for (int i=0; i<index; ++i) pred = pred->next;
        ListNode *p = pred->next;
        pred->next = pred->next->next;
        delete p;
    }
};

/**
 * Your MyLinkedList object will be instantiated and called as such:
 * MyLinkedList* obj = new MyLinkedList();
 * int param_1 = obj->get(index);
 * obj->addAtHead(val);
 * obj->addAtTail(val);
 * obj->addAtIndex(index,val);
 * obj->deleteAtIndex(index);
 */
```
