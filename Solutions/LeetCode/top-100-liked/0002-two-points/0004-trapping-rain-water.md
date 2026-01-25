# Question

[trapping-rain-water](https://leetcode.cn/problems/trapping-rain-water/)



# Answer

> solution

**双指针法解决接雨水问题的核心思路：**

1.  **左右指针**：从数组两端向中间移动，分别代表当前考察的左右边界。
2.  **维护最大值**：`left_max` 和 `right_max` 分别记录左右指针遍历过的柱子中的最大高度。
3.  **短板决定蓄水量**：无论当前柱子高度如何，蓄水量由**当前侧的短板（即左右最大值中较小的那个）**决定。因为水会从短板溢出，所以蓄水量不能超过短板高度。
4.  **移动指针**：如果 `left_max < right_max`，说明左侧是短板，那么左侧指针所指的柱子能蓄的水量就是 `left_max - height[left_ptr]`，然后左指针右移。反之亦然。
5.  **终止条件**：当左右指针相遇时，所有柱子都已考察完毕，蓄水量计算完成。

**关键洞察**：水总是从短板侧溢出，因此我们只需要关注当前侧的短板高度，无需知道另一侧的具体形状。这个思路将问题简化为一次遍历，无需额外数据结构。

### Way

> source code

```c++
class Solution {
public:
    int trap(std::vector<int>& height) {
        int n = height.size();

        // 边界条件：空数组或单柱，无法接水
        if (n <= 1) {
            return 0;
        }

        int res = 0;
        int left_ptr = 0;
        int right_ptr = n - 1;
        int left_max = 0;
        int right_max = 0;

        while (left_ptr < right_ptr) {
            // 更新左右两侧的最高柱子
            left_max = std::max(height[left_ptr], left_max);
            right_max = std::max(height[right_ptr], right_max);

            // 核心逻辑：哪边是短板，就从哪边计算蓄水量并移动指针
            if (left_max < right_max) {
                res += left_max - height[left_ptr];
                left_ptr++;
            } else {
                res += right_max - height[right_ptr];
                right_ptr--;
            }
        }

        return res;
    }
};
```
