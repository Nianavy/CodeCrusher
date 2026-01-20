# Question

[group-anagrams](https://leetcode.cn/problems/group-anagrams/)



# Answer

> solution

**解决思路总结**

利用排序创建异位词的统一签名：
每个字符串的字符排序结果相同即为一组。
用哈希表以排序后的字符串为键，原始字符串为值存储。
最后遍历哈希表收集所有值。

### Way

> source code

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> hash; // 使用 C++ STL 的 unordered_map

        // 遍历输入字符串列表
        for (auto& str: strs) {
            string nstr = str; // 复制原始字符串
            sort(nstr.begin(), nstr.end()); // 对副本进行排序，生成规范化键
            hash[nstr].push_back(str); // 将原始字符串添加到对应键的列表中
        }

        vector<vector<string>> res;
        // 遍历哈希表，将所有值（即异位词列表）收集到结果集中
        for (auto& item : hash) res.push_back(item.second);

        return res; // 返回结果
    }
};
```
