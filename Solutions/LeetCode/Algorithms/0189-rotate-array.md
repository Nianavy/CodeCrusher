# Question

[rotate-array](https://leetcode.cn/problems/rotate-array/)



# Answer

> solution

**核心思路：逆向三部曲**
1. **整体反转**：彻底改变数组的宏观顺序。
2. **反转前段**：让原本在末尾的排序正确元素（后`k`个）回到初始位置。
3. **反转后段**：让原本在前段的末尾元素（前`n-k`个）回到轮转后的位置。
**关键点**：空数组和`k=0`时立即返回，避免无效计算。

### Way Of C

> source code

```c
// 辅助函数：反转数组中 [lo, hi] 范围内的元素
static void reverse(int *nums, int lo, int hi)
{
    while (lo < hi) {
        int tmp = nums[lo];
        nums[lo] = nums[hi];
        nums[hi] = tmp;
        lo++;
        hi--;
    }
}
// 主函数：轮转数组
void rotate(int* nums, int numsSize, int k)
{
    // 1. 守卫语句：处理零长度数组，避免除以零
    if (numsSize == 0) {
        return;
    }
    // 2. 计算实际的旋转量
    k %= numsSize;
    if (k == 0) {
        return; // 没有旋转，直接返回
    }
    // 3. 执行三步反转
    reverse(nums, 0, numsSize - k - 1);
    reverse(nums, numsSize - k, numsSize - 1);
    reverse(nums, 0, numsSize - 1);
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        if (nums.empty()) { // 第一道防线：处理空数组
            return;
        }
        int n = nums.size();
        k %= n;
        // 三步反转逻辑：先整体反转，再反转前k个，再反转后n-k个
        reverse(nums.begin(), nums.end());
        reverse(nums.begin(), nums.begin() + k);
        reverse(nums.begin() + k, nums.end());
    }
};
```
