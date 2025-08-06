https://www.acwing.com/problem/content/5083/

### Think
```
Use version difference for efficient range statistical queries.
```

### Ways
```C++
#include <iostream>
#include <vector>
#include <algorithm>

typedef long long LL;
std::vector<int> nums;
const int N = 1e5 + 10;
int n, m, a[N], root[N], idx;
struct Node {
  int l, r, cnt;
  LL sum;
}tr[N * 4 + 17 * N];

int find(int x) {
  return std::lower_bound(nums.begin(), nums.end(), x) - nums.begin();
}

void pushup(Node &u, Node &l, Node &r) {
  u.sum = l.sum + r.sum;
  u.cnt = l.cnt + r.cnt;
}

void pushup(int u) {
  pushup(tr[u], tr[tr[u].l], tr[tr[u].r]);
}

int build(int l, int r) {
  int p = ++idx;
  if (l == r) return p;
  int mid = (l + r) >> 1;
  tr[p].l = build(l, mid), tr[p].r = build(mid + 1, r);
  return p;
}

int insert(int p, int l, int r, int x) {
  int q = ++idx;
  tr[q] = tr[p];
  if (l == r) {
    tr[q].cnt++;
    tr[q].sum += nums[x];
    return q;
  }
  int mid = (l + r) >> 1;
  if (x <= mid) tr[q].l = insert(tr[p].l, l, mid, x);
  else tr[q].r = insert(tr[p].r, mid + 1, r, x);
  pushup(q);
  return q;
}

int query_kth(int p, int q, int l, int r, int k) {
  if (l == r) return nums[l];
  int mid = (l + r) >> 1;
  int cnt = tr[tr[q].l].cnt - tr[tr[p].l].cnt;
  if (k <= cnt) return query_kth(tr[p].l, tr[q].l, l, mid, k);
  else return query_kth(tr[p].r, tr[q].r, mid + 1, r, k - cnt);
}

Node query_sum(int p, int q, int l, int r, int TL, int TR) {
    Node res;
    if (l >= TL && r <= TR) 
        return res = {l, r, tr[q].cnt - tr[p].cnt, tr[q].sum - tr[p].sum};

    int mid = (l + r) >> 1;
    if (TR <= mid) return query_sum(tr[p].l, tr[q].l, l, mid, TL, TR);
    else if (TL > mid) return query_sum(tr[p].r, tr[q].r, mid + 1, r, TL, TR);
    else {
        auto left = query_sum(tr[p].l, tr[q].l, l, mid, TL, TR);
        auto right = query_sum(tr[p].r, tr[q].r, mid + 1, r, TL, TR);
        pushup(res, left, right);
        return res;
    }
}

Node query_sum(int p, int q, int l, int r) {
    return query_sum(p, q, 0, nums.size() - 1, l, r);
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i ++) {
        scanf("%d", &a[i]);
        nums.push_back(a[i]);
    }

    sort(nums.begin(), nums.end());
    nums.erase(unique(nums.begin(), nums.end()), nums.end());
    root[0] = build(0, nums.size() - 1);

    for (int i = 1; i <= n; i ++) 
        root[i] = insert(root[i - 1], 0, nums.size() - 1, find(a[i]));

    scanf("%d", &m);
    while (m --) {
        int l, r;
        scanf("%d%d", &l, &r);
        int kth = query_kth(root[l - 1], root[r], 0, nums.size() - 1, (l + r) / 2 - l + 1);
        auto left = query_sum(root[l - 1], root[r], 0, find(kth));
        Node right = {0, 0, tr[root[r]].cnt - tr[root[l - 1]].cnt - left.cnt, tr[root[r]].sum - tr[root[l - 1]].sum - left.sum};
        printf("%lld\n", ((LL)left.cnt * kth) - left.sum + right.sum - ((LL)right.cnt * kth));
    }
    return 0;
}
```
