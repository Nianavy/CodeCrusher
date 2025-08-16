https://www.acwing.com/problem/content/5077/

```txt
Sort users by weight,
traverse the tree via BFS,
and assign users sequentially to leaf nodes.
```


```C++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>

const int N = 310;
int n, tr[N][3], w[N], p[N], cnt;

void bfs();

int main() {
    scanf("%d", &n);
    memset(tr, -1, sizeof(tr));  // 初始化为 -1，表示无子节点

    // 读取每个节点的子节点信息
    for (int i = 0; i < n; ++i) {
        int t;
        scanf("%d", &t);
        for (int j = 0; j < 3; ++j) {
            scanf("%d", &tr[t][j]);
        }
    }

    int num, flow;
    cnt = 0;
    // 读取有权重的节点（分配目标）
    while (scanf("%d %d", &num, &flow) == 2) {
        w[num] = flow;
        p[cnt++] = num;
    }

    // 按权重降序，编号升序排序
    std::sort(p, p + cnt, [&](int a, int b) {
        if (w[a] != w[b]) return w[a] > w[b];
        return a < b;
    });

    bfs();

    return 0;
}

void bfs() {
    std::queue<int> q;
    q.push(100);  // 根节点为 100

    int k = 0;  // p 数组的当前分配索引

    while (!q.empty()) {
        int t = q.front();
        q.pop();

        // 判断是否为叶子节点：三个子节点都为 -1
        bool is_leaf = true;
        for (int i = 0; i < 3; ++i) {
            if (tr[t][i] != -1) {
                q.push(tr[t][i]);
                is_leaf = false;
            }
        }

        // 如果是叶子节点，并且还有人未分配
        if (is_leaf && k < cnt) {
            printf("%d %d\n", p[k++], t);
        }
    }
}
```
