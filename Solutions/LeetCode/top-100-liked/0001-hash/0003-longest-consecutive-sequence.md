# Question

[longest-consecutive-sequence](https://leetcode.cn/problems/longest-consecutive-sequence/)



# Answer

> solution

1.  **数据预处理：去重与快速查找**
    *   将输入数组 `nums` 的所有数字放入一个哈希表 `unordered_set<int> s` 中。这一步消除了重复数字的干扰，并提供了 **O(1)** 时间复杂度的成员查询能力。

2.  **避免重复计算：遍历哈希表，而非数组**
    *   直接遍历哈希表中的元素。因为哈希表已经去重，遍历次数取决于数字的唯一性，而非数组长度。

3.  **关键优化：只从序列的起点开始计数**
    *   对于哈希表中的每个数字 `n`，判断其前驱 `n-1` 是否存在于哈希表中。
    *   **如果 `n-1` 不存在**：意味着 `n` 是一个连续序列的起始点。这一步是算法的核心，确保了**每条连续序列只被计算一次**。
    *   **如果 `n-1` 存在**：则 `n` 是某个连续序列的中间或末尾数字，直接跳过，不做计数。

4.  **计算当前连续序列的长度**
    *   仅当 `n` 是序列的起点时，以 `n` 为起点，向后连续查找 `n+1, n+2, n+3...` 存在于哈希表中，直到断开为止。
    *   统计连续存在的数量，作为该序列的长度。

5.  **更新全局最大值**
    *   将当前序列的长度与全局最大值 `res` 比较并更新。

6.  **返回结果**
    *   遍历结束后，`res` 即为最长连续序列的长度。

### Way

> source code

```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        // 将输入数组去重并存入哈希表，以实现 O(1) 的查找时间复杂度
        unordered_set<int> s(nums.begin(), nums.end());
        int res = 0;
        
        // 遍历哈希表中的每个数字
        for (int n : s) {
            // 核心优化：只从连续序列的起始点（即没有前驱 n-1 的点）开始计算
            // 这确保了每条连续序列只被计算一次，避免重复计算
            if (!s.count(n - 1)) {
                int len = 1;
                // 向后探查，计算当前连续序列的长度
                while (s.count(n + len)) {
                    len++;
                }
                // 更新最大长度
                res = max(res, len);
            }
        }
        return res;
    }
};
```
