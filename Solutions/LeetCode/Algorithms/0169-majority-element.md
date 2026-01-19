# Question

[majority-element](https://leetcode.cn/problems/majority-element/)



# Answer

> solution

**核心：Boyer-Moore投票算法**

**思路**：
1. 初始化元素为第一个数，计数为1。
2. 遍历数组：
   - 若计数为0，重置候选为当前元素，计数为1。
   - 若当前元素等于候选，计数加1。
   - 否则，计数减1。
3. 遍历结束，候选即为结果（题目保证存在多数元素）。

**本质**：用计数器模拟投票和抵消过程，多数元素最终会胜出。

### Way Of C

> source code

```c
int majorityElement(int* nums, int numsSize) {
    if (numsSize == 0) return -1; // 防御性编程
    
    int major = nums[0];
    int count = 1;
    
    for (int i = 1; i < numsSize; i++) {
        if (count == 0) {
            major = nums[i];
            count = 1;
        } else if (nums[i] == major) {
            count++;
        } else {
            count--;
        }
    }
    return major;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int major = nums[0];
        int count = 1;
        for (int i = 1; i < nums.size(); i++) {
            if (count == 0) {
                major = nums[i];
                count = 1;
            } else if (nums[i] == major) {
                count++;
            } else {
                count--;
            }
        }
        return major;
    }
};
```
