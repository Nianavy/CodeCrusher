# Question

[longest-consecutive-sequence](https://leetcode.cn/problems/longest-consecutive-sequence/)



# Answer

> solution

【问题本质】
找最长连续序列，要求O(n)时间复杂度。

【解决思路】

第一层：算法选择
贪心 + 哈希集合。从每个序列的起点开始，向后延伸找最长长度。

第二层：数据结构
哈希集合：O(1)查询数字是否存在
去重很关键：遍历集合而非原数组，避免重复处理

第三层：核心逻辑
1. 将所有数字放入哈希集合（自动去重）
2. 遍历集合中的每个数字 n：
   - 若 n-1 不存在，说明 n 是某个序列的起点
   - 从 n 开始，逐个检查 n+1, n+2, ... 是否存在
   - 记录最长长度
3. 返回最长长度

第四层：时间复杂度分析
每个数字最多被访问一次（作为序列起点时）
每次查询O(1)
总复杂度O(n)

### Way Of C

> source code

```c
int cmp(const void *a, const void *b) {
    int x = *(int*)a, y = *(int*)b;
    return x < y ? -1 : (x > y ? 1 : 0);
}

int longestConsecutive(int* nums, int numsSize) {
    if (numsSize == 0) return 0;
    
    qsort(nums, numsSize, sizeof(int), cmp);
    
    int res = 1, len = 1;
    for (int i = 1; i < numsSize; i++) {
        if (nums[i] == nums[i-1]) continue;
        if (nums[i] == nums[i-1] + 1) {
            len++;
        } else {
            res = len > res ? len : res;
            len = 1;
        }
    }
    return len > res ? len : res;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> s(nums.begin(), nums.end());
        int res = 0;
        
        for (int n : s) {
            if (!s.count(n - 1)) {
                int len = 1;
                while (s.count(n + len)) {
                    len++;
                }
                res = max(res, len);
            }
        }
        return res;
    }
};
```
