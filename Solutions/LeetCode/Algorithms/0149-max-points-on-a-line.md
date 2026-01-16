# Question

[max-points-on-a-line](https://leetcode.cn/problems/max-points-on-a-line/)



# Answer

> solution

> **“枚举每个点作为参考点，对于每个参考点，将其他所有点转化为相对于该点的方向向量，通过最大公约数和符号统一规则将其归一化，然后使用哈希表统计出现次数。最终答案是 `max(哈希表最大计数 + 重复点数, 重复点数)` 的全局最大值。”**

这个思路**避免了浮点数误差**，**统一了所有特殊情况（重复点、垂直线）**，并且**逻辑清晰、可维护**。它体现了“好品味”：用最直接的数据结构（整数对）解决最本质的问题（方向的比例关系）。</think>## **【解决思路总结】**

### **1. 核心洞察**
- **帮定：枚举每个点作为基准，其他点与它的关系决定共线性。**
- **关键：关系必须用可靠方式表示，浮点数斜率不可靠。**

### **2. 正确数据结构**
- **错误：** 用 `long double` 表示斜率存入哈希表 → **浮点精度导致计数失效**。
- **正确：** 用**整数方向向量** `(dx, dy)` 表示相对方向。
- **标准化：** 通过最大公约数(GCD)约分向量，并统一符号。
  - 例如：`(2,4)` → GCD=2 → 归一化为 `(1,2)`
  - 符号统一：`( -1, -2 )` → 统一为 `(1,2)`，表示同一直线

### **3. 统一所有情况的算法**
对于每个基准点 `p`：
1.  **初始化**：设 `dup = 1`（考虑重复点，即 `p` 自身）
2.  **遍历其他点**：对每个点 `q`
    - 若 `q == p`：`dup++`
    - 否则：
        - 计算方向向量 `(dx, dy) = (q.x-p.x, q.y-p.y)`
        - 计算 GCD，约分 `(dx, dy)`
        - 统一符号（如：`dx<0` 或 `dx==0 && dy<0` 时取反）
        - 计数器：`map[(dx,dy)]++`
3.  **计算当前最大**：`max( dup, max( map[value] ) + dup )`
4.  **全局最大**：所有基准点的上一步结果取最大

### **4. 为什么更优？**
- **消除特殊情况**：重复点（`dup`）、垂直线？都在同一流水线处理
- **绝对精确**：整数运算无误差
- **逻辑清晰**：每个点处理方式一致，代码更简洁

### **5. 复杂度**
- **时间**：O(N²) → 必然，必须检查每对点
- **空间**：O(N) → 哈希表存储方向向量

### **6. 一句话概括**
> **“对每个点，计算所有其他点的归一化方向向量并计数，最大计数即答案。”**

### Way Of C

> source code

```c
// 简单的哈希表节点，用于存储方向向量和计数
// 这里我们自己实现一个极简的哈希表（因为C标准库没有unordered_map）
typedef struct HashNode {
    int dx;
    int dy;
    int count;
    struct HashNode* next;
} HashNode;

// 哈希函数：对方向向量编码后的值进行哈希
// 方向向量(dx, dy) 已归一化，dx, dy 在合理范围内
int hash_func(int dx, int dy) {
    // 简单哈希，实际应用中可能需要考虑碰撞
    // 使用一个素数乘以 dx 再加上 dy
    // 由于 dx, dy 是归一化后的值，它们的绝对值不会太大（理论上可能大，但实际点坐标有限）
    return ((dx * 131) + dy) & 0x7FFFFFFF;
}

// 创建新节点
HashNode* create_node(int dx, int dy, int count) {
    HashNode* node = (HashNode*)malloc(sizeof(HashNode));
    node->dx = dx;
    node->dy = dy;
    node->count = count;
    node->next = NULL;
    return node;
}

// 释放哈希表内存
void free_hash_table(HashNode** hash_table, int table_size) {
    for (int i = 0; i < table_size; ++i) {
        HashNode* curr = hash_table[i];
        while (curr) {
            HashNode* temp = curr;
            curr = curr->next;
            free(temp);
        }
    }
    free(hash_table);
}

// 最大公约数
int gcd(int a, int b) {
    while (b) {
        int temp = a % b;
        a = b;
        b = temp;
    }
    return a;
}

// 主函数
int maxPoints(int** points, int pointsSize, int* pointsColSize) {
    // 检查输入有效性
    if (pointsSize <= 2) {
        return pointsSize;
    }

    int res = 0;

    // 对于每一个点作为参考点 p
    for (int i = 0; i < pointsSize; ++i) {
        // 创建一个哈希表存储方向 (dx, dy) -> count
        // 这里使用一个较大点数的哈希表以避免频繁扩容和过多的链表
        const int HASH_TABLE_SIZE = 5000; // 调整这个大小以平衡性能
        HashNode** hash_table = (HashNode**)calloc(HASH_TABLE_SIZE, sizeof(HashNode*));
        if (!hash_table) {
            // 内存分配失败，应处理错误，这里简化
            return -1;
        }

        int zero = 1; // 重复点数（包括自己）

        // 遍历所有其他点
        for (int j = 0; j < pointsSize; ++j) {
            if (i == j) {
                // zero 在循环外初始化为1，这里不再增加
                continue;
            }

            // 计算方向向量
            int dx = points[j][0] - points[i][0];
            int dy = points[j][1] - points[i][1];

            // 归一化：计算 GCD 并除以它
            int g = gcd(abs(dx), abs(dy));
            dx /= g;
            dy /= g;

            // 统一符号：让 dx 恒为正；如果 dx 为 0，则让 dy 为正
            if (dx < 0 || (dx == 0 && dy < 0)) {
                dx = -dx;
                dy = -dy;
            }

            // 在哈希表中查找并更新计数
            int hash = hash_func(dx, dy) % HASH_TABLE_SIZE;
            HashNode* node = hash_table[hash];
            HashNode* prev = NULL;

            // 查找是否存在相同的 (dx, dy)
            while (node) {
                if (node->dx == dx && node->dy == dy) {
                    node->count++;
                    break;
                }
                prev = node;
                node = node->next;
            }

            // 如果没有找到，插入新节点
            if (!node) {
                HashNode* new_node = create_node(dx, dy, 1); // 初始计数为1
                if (prev) {
                    prev->next = new_node;
                } else {
                    hash_table[hash] = new_node;
                }
            }
        }

        // 计算当前点 i 的最大共线点数
        int local_max = zero; // 至少包括自己和重复点
        for (int k = 0; k < HASH_TABLE_SIZE; ++k) {
            HashNode* curr = hash_table[k];
            while (curr) {
                if (curr->count > 0) {
                    int points_on_line = curr->count + zero; // 加上重复点
                    if (points_on_line > local_max) {
                        local_max = points_on_line;
                    }
                }
                curr = curr->next;
            }
        }

        // 更新全局最大值
        if (local_max > res) {
            res = local_max;
        }

        // 释放当前 i 的哈希表内存
        free_hash_table(hash_table, HASH_TABLE_SIZE);
    }

    return res;
}
```

---

### Way Of C++

> source code

```c++
// 关键：所有点都视为相对于 p 的方向向量。重复点就是 (0,0)。
// 算法：对于每个 p，计算所有 q 的方向，用最简整数对表示。
// 使用 map 计数。max = max(计数) + 自己。

class Solution {
public:
    int maxPoints(vector<vector<int>>& points) {
        int n = points.size();
        int res = 0;

        for (int i = 0; i < n; ++i) {
            // map 使用 int-pair 作为键，但 C++ 不能直接用 pair 做键。
            // 我们用字符串编码： "dx,dy"
            unordered_map<string, int> cnt;
            int zero = 0; // 重复点计数（包括自己）

            for (int j = 0; j < n; ++j) {
                if (i == j) {
                    zero++;
                    continue;
                }

                int dx = points[j][0] - points[i][0];
                int dy = points[j][1] - points[i][1];

                // 归一化：除以 gcd，并将符号统一（dx < 0 或 dx == 0 && dy < 0 时取反）
                int g = gcd(abs(dx), abs(dy));
                dx /= g;
                dy /= g;

                if (dx < 0 || (dx == 0 && dy < 0)) {
                    dx = -dx;
                    dy = -dy;
                }

                string key = to_string(dx) + "," + to_string(dy);
                cnt[key]++;
            }

            int local_max = zero;
            for (const auto& kv : cnt) {
                local_max = max(local_max, kv.second + zero);
            }
            res = max(res, local_max);
        }
        return res;
    }

    // 辅助函数：计算最大公约数
    int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }
};
```
