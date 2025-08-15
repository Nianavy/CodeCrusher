https://leetcode.cn/problems/two-sum

### think:

1. 遍历数组中的每个元素。
2. 对于每个元素，检查是否存在一个已经遍历过的元素，使得这两个元素的和等于`target`。
3. 如果找到了这样的元素对，立即返回它们的索引。
4. 如果没有找到，将当前元素及其索引存入哈希表，以便后续查找。
5. 如果遍历了整个数组都没有找到，则返回空数组。



### ways:

By C++:

```cpp
class Solution {
    public:
        vector<int> twoSum(vector<int> &nums, int target) {
            unordered_map<int, int> hashtable;
            for (int i=0; i<nums.size(); ++i) {
                auto it = hashtable.find(target - nums[i]);
                if (it != hashtable.end())
                    return {it->second, i};
                hashtable[nums[i]] = i;
            }
            return {};
        }
};
```

