https://leetcode.cn/problems/move-zeroes

### Think
```TXT
使用双指针，左指针指向当前已经处理好的序列的尾部，右指针指向待处理序列的头部。

右指针不断向右移动，每次右指针指向非零数，则将左右指针对应的数交换，同时左指针右移。

注意到以下性质：
左指针左边均为非零数；
右指针左边直到左指针处均为零。

因此每次交换，都是将左指针的零与右指针的非零数交换，且非零数的相对顺序并未改变。

链接：https://leetcode.cn/problems/move-zeroes/solutions/489622/yi-dong-ling-by-leetcode-solution/
```

### Ways
By C++
```C++
class Solution {
public:
  void moveZeroes(vector<int> &nums) {
    int n = nums.size(), left = 0, right = 0;
    while (right < n) {
      if (nums[right]) {
        swap(nums[left], nums[right]);
        left++;
      }
      right++;
    }
  }
};
```
