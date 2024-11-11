https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii

### think:

双指针

### ways:
By C++:
```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int n = nums.size();  // 获取输入数组的大小（元素个数）
        if (n <= 2) {  // 如果数组的大小小于或等于 2，直接返回 n，因为最多两个的重复是允许的
            return n;
        }
        
        int slow = 2, fast = 2;  // 初始化两个指针，slow 和 fast 都从索引 2 开始
                                 // slow 指针用于构建结果数组，fast 指针用于遍历整个数组
        
        while (fast < n) {  // 开始遍历数组，从索引 2 开始检查每一个元素
            if (nums[slow - 2] != nums[fast]) {  // 如果 slow 指针前移两个位置的元素不等于 fast 指针指向的元素
                                                 // 这意味着当前 fast 指向的元素可以被保留，因为它和前面已经处理过的元素没有构成超过两次的重复
                nums[slow] = nums[fast];  // 将 fast 指向的元素复制到 slow 指向的位置
                ++slow;  // slow 指针右移一位，准备处理下一个位置
            }
            ++fast;  // 无论条件是否成立，fast 指针都会右移一位，继续遍历下一个元素
        }
        
        return slow;  // 循环结束后，slow 指针的位置就是处理后不重复元素数组的长度
    }
};
```
