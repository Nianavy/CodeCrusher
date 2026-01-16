# Question

[lru-cache](https://leetcode.cn/problems/lru-cache/)



# Answer

> solution

C++ 版本 (STL 实现)
核心思路：

数据结构：std::list 维护双向链表，std::unordered_map 贮存 key 到链表迭代器的映射
查找：通过哈希表 O(1) 定位节点
LRU 维护：
get()：命中后，从原位置删除 list.erase()，再 push_front 到头部
put()：插入前，删除同 key 节点；如果容量满，删除链表尾部节点
特点：利用 STL 容器的迭代器持久性，无需手动内存管理
时间复杂度：O(1) 一次操作

空间复杂度：O(N)

C 版本 (手动实现)
核心思路：

数据结构：双链表 + 分离链哈希表
Node 结构体：同时存在于双链表（prev/next）和哈希桶（hash_next）
LRUCache：管理 head/tail 双向链表 + Node** buckets 哈希表
LRU 维护：
get()：在哈希表找到节点 → 从双链表移除 → 插入头部
put()：先检查是否存在 → 删除旧节点（如果存在）→ 容量满时删除尾部节点 → 插入新节点
内存管理：手动 malloc/free，需注意释放顺序和边界
时间复杂度：O(1) 一次操作

空间复杂度：O(N)

本质相同：都是通过哈希表快速定位，通过链表维护 LRU 顺序，都是经典 LRU 设计

### Way Of C

> source code

```c
typedef struct Node {
    int key, value;
    struct Node *prev, *next;
    struct Node *hash_next;  // 哈希冲突链
} Node;
typedef struct {
    int capacity, size;
    Node *head, *tail;        // 双向链表：0->tail (LRU -> MRU) -> head
    Node **buckets;           // 哈希桶
    int bucket_count;
} LRUCache;
// 简单哈希函数
static int hash(int key, int mod) {
    return (key + 0x7ABCDEF) % mod; // 避免负数问题
}
// 创建节点
static Node* createNode(int key, int value) {
    Node *node = malloc(sizeof(Node));
    node->key = key;
    node->value = value;
    node->prev = node->next = NULL;
    node->hash_next = NULL;
    return node;
}
// 从链表中移除节点（不释放）
static void removeFromList(LRUCache *cache, Node *node) {
    if (!node) return;
    if (node->prev) node->prev->next = node->next;
    if (node->next) node->next->prev = node->prev;
    if (cache->head == node) cache->head = node->next;
    if (cache->tail == node) cache->tail = node->prev;
}
// 将节点插入链表头部（最近使用）
static void insertHead(LRUCache *cache, Node *node) {
    if (!node) return;
    node->prev = NULL;
    node->next = cache->head;
    
    if (cache->head) cache->head->prev = node;
    else cache->tail = node;  // 原本为空
    
    cache->head = node;
}
// 在哈希桶中查找节点
static Node** findInHash(LRUCache *cache, int key, int *hash_code) {
    *hash_code = hash(key, cache->bucket_count);
    Node **slot = &cache->buckets[*hash_code];
    Node *curr = *slot;
    
    while (curr) {
        if (curr->key == key) {
            return slot; // 返回该节点在哈希表中的前驱地址
        }
        slot = &curr->hash_next;
        curr = curr->hash_next;
    }
    return NULL;
}
LRUCache* lRUCacheCreate(int capacity) {
    if (capacity <= 0) return NULL;
    
    LRUCache *cache = malloc(sizeof(LRUCache));
    cache->capacity = capacity;
    cache->size = 0;
    cache->head = cache->tail = NULL;
    
    // 哈希桶数量设为容量的2倍+1，减少冲突
    cache->bucket_count = capacity * 2 + 1;
    cache->buckets = calloc(cache->bucket_count, sizeof(Node*));
    
    return cache;
}
int lRUCacheGet(LRUCache *cache, int key) {
    if (!cache) return -1;
    
    int hash_code;
    Node **slot = findInHash(cache, key, &hash_code);
    
    if (slot && *slot) {
        Node *node = *slot;
        int value = node->value;
        
        // 移动到链表头部
        removeFromList(cache, node);
        insertHead(cache, node);
        
        return value;
    }
    return -1;
}
void lRUCachePut(LRUCache *cache, int key, int value) {
    if (!cache || cache->capacity == 0) return;
    
    int hash_code;
    Node **slot = findInHash(cache, key, &hash_code);
    
    // 如果键已存在
    if (slot && *slot) {
        Node *node = *slot;
        node->value = value;
        
        // 移动到链表头部
        removeFromList(cache, node);
        insertHead(cache, node);
        return;
    }
    
    // 容量已满，删除LRU节点
    if (cache->size == cache->capacity) {
        Node *oldTail = cache->tail;
        
        // 从链表中移除
        removeFromList(cache, oldTail);
        
        // 从哈希表中移除旧节点
        int old_hash;
        Node **old_slot = findInHash(cache, oldTail->key, &old_hash);
        if (old_slot && *old_slot == oldTail) {
            *old_slot = oldTail->hash_next;
        }
        
        // 释放旧节点
        free(oldTail);
        cache->size--;
    }
    
    // 创建新节点
    Node *newNode = createNode(key, value);
    
    // 加入链表头部
    insertHead(cache, newNode);
    
    // 加入哈希表
    newNode->hash_next = cache->buckets[hash_code];
    cache->buckets[hash_code] = newNode;
    
    cache->size++;
}
void lRUCacheFree(LRUCache *cache) {
    if (!cache) return;
    
    // 释放所有节点
    Node *curr = cache->head;
    while (curr) {
        Node *next = curr->next;
        free(curr);
        curr = next;
    }
    
    // 释放哈希桶数组
    free(cache->buckets);
    
    // 释放结构体
    free(cache);
}
/**
 * Your LRUCache struct will be instantiated and called as such:
 * LRUCache* obj = lRUCacheCreate(capacity);
 * int param_1 = lRUCacheGet(obj, key);
 
 * lRUCachePut(obj, key, value);
 
 * lRUCacheFree(obj);
*/
```

---

### Way Of C++

> source code

```c++
class LRUCache {
private:
    int cap;
    // 双向链表，存储(key, value)，空间是最近使用的在前
    std::list<std::pair<int, int>> dlist;
    // 哈希表，key指向链表中的节点
    std::unordered_map<int, std::list<std::pair<int, int>>::iterator> hash;
public:
    LRUCache(int capacity) : cap(capacity) {}
    int get(int key) {
        // 哈希表中存在即命中
        if (hash.find(key) != hash.end()) {
            // 获取当前值
            int value = hash[key]->second;
            // 将键值对移到链表头部
            dlist.erase(hash[key]);
            dlist.push_front({key, value});
            // 更新哈希映射
            hash[key] = dlist.begin();
            return value;
        }
        return -1;
    }
    void put(int key, int value) {
        // 存在先删除
        if (hash.find(key) != hash.end()) {
            dlist.erase(hash[key]);
        }
        // 容量满则删除尾部
        else if (dlist.size() == cap) {
            hash.erase(dlist.back().first);
            dlist.pop_back();
        }
        // 插入头部
        dlist.push_front({key, value});
        hash[key] = dlist.begin();
    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```
