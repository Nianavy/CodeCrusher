# Question

[single-number](https://leetcode.cn/problems/single-number/)



# Answer

> solution

【解决思路】

**问题本质**
数组中除一个元素外，其余每个元素均出现**两次**。找出只出现一次的元素。

**数学洞察**
XOR（异或）运算的核心性质：
1. **自反性**：`a ^ a = 0`
2. **恒等性**：`a ^ 0 = a`
3. **交换律**：`a ^ b ^ a = a ^ a ^ b = 0 ^ b = b`

**算法核心**
```
初始化：res = 0
遍历：res ^= 每个元素
返回：res
```

**为什么有效**
- 成对出现的元素：`a ^ b ^ a ^ b ^ c = (a ^ a) ^ (b ^ b) ^ c = 0 ^ 0 ^ c = c`
- 最终结果 = 所有元素的XOR = 唯一元素

**复杂度**
- **时间**：O(n) - 单次遍历
- **空间**：O(1) - 只用一个变量

**边界情况**
- 空数组：返回0（或需定义）
- 单个元素：返回该元素

**本质**
这是**线性时间、常数空间**的最优解，利用XOR的代数性质消除配对。

### Way Of C

> source code

```c
int singleNumber(int* nums, int numsSize) {
    if (numsSize == 0) return 0;  // 防御性编程
    
    int res = 0;
    for (int i = 0; i < numsSize; ++i) {
        res ^= nums[i];
    }
    return res;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int res = 0;
        for (int num : nums) {
            res ^= num;
        }
        return res;
    }
};
```
