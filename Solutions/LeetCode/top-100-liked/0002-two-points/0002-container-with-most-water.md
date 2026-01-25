# Question

[container-with-most-water](https://leetcode.cn/problems/container-with-most-water/)



# Answer

> solution

**算法思路解析：**

1.  **问题本质**：在给定的高度数组中，寻找两个下标 i 和 j，使得它们构成的矩形面积最大（面积 = min(h[i], h[j]) * (j - i)）。
2.  **双指针策略**：初始设置 i=0, j=n-1（边界最远，宽度最大）。通过计算当前边界面积，并更新最大值。
3.  **指针移动原则**：每次只移动较短的边对应的指针。
    *   **为什么移动短边？** 移动短边存在找到更高墙的可能，从而可能找到更大的面积（即使宽度变小）。而移动长边只会缩小宽度，且目标高度（备选高度）不会超过当前长边的高度，因此一定不会得到更大的面积。
4.  **优化原理**：算法通过移动指针“主动排除”了不可能产生更优解的区域，将问题从 O(n²) 变为 O(n)。

### Way

> source code

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int res = 0;
        // 使用双指针 i（左）和 j（右）遍历边界
        for (int i = 0, j = height.size() - 1; i < j;) {
            // 计算当前边界构成的矩形面积
            // 高度：min(height[i], height[j])
            // 宽度：j - i
            res = max(res, min(height[i], height[j]) * (j - i));
            
            // 移动双指针的核心逻辑
            if (height[i] > height[j]) {
                --j;  // 右指针内缩（因为移动左指针可能得到更高墙，但右指针当前是短板）
            } else {
                ++i;  // 左指针右移（同理，寻找更长边）
            }
        }
        return res;
    }
};
```
