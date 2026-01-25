# Question

[move-zeroes](https://leetcode.cn/problems/move-zeroes/)



# Answer

> solution

解决移动零问题的正确方式：

使用双指针：
让一个指针 non_zero_idx 指向“下一个非零元素应该被放置的位置”。
遍历数组，每当遇到一个非零元素，就把它复制到 nums[non_zero_idx] 并递增 non_zero_idx。
遍历结束后，所有非零元素都已经按顺序放在了数组的前部。

填充零：
在第一次遍历后，从 non_zero_idx 开始到数组末尾，全部填充为 0。这只需要一次线性遍历。

确保向后兼容：
这个方法不会破坏任何现有功能。它只是原地修改数组。

### Way

> source code

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int idx = 0;
        // 第一步：把所有非零元素移到数组前面
        for (int n : nums) {
            if (n != 0) {
                nums[idx++] = n;
            }
        }
        // 第二步：把剩下的位置填成零
        while (idx < nums.size()) {
            nums[idx++] = 0;
        }
    }
};
```
