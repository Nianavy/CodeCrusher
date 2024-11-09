
### think:

双指针

### ways:
By C++:
```cpp
class Solution {
public:
    int removeElement(vector<int> &nums, int val) {
        // 获取数组的长度
        int n = nums.size();
        // 定义左指针，初始指向数组的起始位置
        int left = 0;
        
        // 遍历数组，右指针从左到右移动
        for (int right = 0; right < n; right++) {
            // 如果当前右指针指向的元素不等于需要移除的值
            if (nums[right] != val) {
                // 将当前元素移到左指针的位置
                nums[left] = nums[right];
                // 左指针向右移动一位
                left++;
            }
        }
        // 返回左指针的位置，即为新数组的长度
        return left;
    }
};

```
