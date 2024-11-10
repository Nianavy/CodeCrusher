https://leetcode.cn/problems/remove-duplicates-from-sorted-array

### think:

双指针

### ways:
By C++:
```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        // 获取数组的长度
        int n = nums.size();
        
        // 如果数组为空，直接返回0
        if (n == 0) {
            return 0;
        }
        
        // 初始化两个指针，fast用于遍历数组，slow用于记录不重复元素的位置
        int fast = 1, slow = 1;
        
        // 遍历数组，从第二个元素开始
        while (fast < n) {
            // 如果当前元素与前一个元素不同
            if (nums[fast] != nums[fast - 1]) {
                // 将当前元素放到slow指针的位置
                nums[slow] = nums[fast];
                // slow指针向后移动一位
                ++slow;
            }
            // fast指针向后移动一位，继续遍历
            ++fast;
        }
        
        // 返回不重复元素的长度
        return slow;
    }
};
```
