# Question

[two-sum](https://leetcode.cn/problems/two-sum/description/?envType=study-plan-v2&envId=top-100-liked/)



# Answer

> solution

**总结**

解决思路极其简单：

1.  **数据结构**：使用一个哈希表（unordered_map）来存储每个元素值到其索引的映射。
2.  **遍历**：遍历数组。对于每个元素 `nums[i]`，计算其补数 `other = target - nums[i]`。
3.  **查找**：检查补数 `other` 是否已在哈希表中存在。
    *   **如果存在**：直接返回其索引 `store[other]` 和当前索引 `i`。
    *   **如果不存在**：将当前元素及其索引 `nums[i]` -> `i` 存入哈希表，继续遍历。
4.  **终止**：该算法保证在遍历过程中一定能在哈希表中找到补数对，因此遍历结束前必有返回。

核心是 **一次哈希查找** 替代了嵌套循环的暴力解。

### Way

> source code

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        // 存储最终结果（两个数字的下标）
        vector<int> ans;
        // 建立“值 -> 下标”的哈希映射，用于快速查找补数
        unordered_map<int, int> cnt;
        
        // 遍历数组中的每个元素
        for (int i = 0; i < nums.size(); ++i) {
            // 计算当前数字需要的另一个数字（补数）
            int other = target - nums[i];
            
            // 如果补数已经出现在之前的元素中，我们就找到了答案
            if (cnt.contains(other)) {
                // 将补数的下标（之前存储的）和当前下标推入结果数组
                ans.push_back(cnt[other]);
                ans.push_back(i);
                return ans;
            }
            
            // 将当前数字和它的下标存入映射，供后续数字查找
            cnt[nums[i]] = i;
        }
                  
        return ans;
    }
};
```
