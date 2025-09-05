# Question

[container-with-most-water](https://leetcode.cn/problems/container-with-most-water/)



# Answer

> solution

1.  **确定边界：两根柱子，一左一右。**
    *   初始化两个指针 `i` (左指针) 和 `j` (右指针)，分别指向数组的最左端和最右端。
    *   **理由：** 这样开始时容器的宽度是最大的，我们以此为基础，尝试找到最高的组合。

2.  **循环迭代：不断缩小容器宽度，直到指针相遇。**
    *   循环条件是 `i < j`。一旦 `i >= j`，容器宽度为零或负数，就没有意义了。

3.  **计算当前面积：找出限制，并更新最大值。**
    *   在每次迭代中，计算当前左右指针所形成的容器的面积：`min(height[i], height[j]) * (j - i)`。
    *   用这个面积去更新全局的最大面积 `res = max(res, current_area)`。
    *   **理由：** 较矮的那根线是当前容器的“短板”，决定了水能达到的高度。`j-i` 是宽度。

4.  **移动指针：总是扔掉较矮的那个“短板”。**
    *   这是算法的**核心和精髓**。
    *   **决策逻辑：**
        *   如果 `height[i] > height[j]` (右边更矮)，那么移动右指针 `j--`。
        *   否则 ( `height[i] <= height[j]`，左边更矮或两边一样高)，移动左指针 `i++`。
    *   **为什么只移动较矮的？**
        *   当前容器的容量由 `min(height[i], height[j])` 决定。假设 `height[i]` 是较矮的。
        *   如果移动较高的那个指针（例如 `j--`），那么宽度 `j-i` 肯定会减少。而新的容器高度的短板仍然可能是 `height[i]` (因为它没动，并且它比 `height[j-1]` 小)。这样，面积很可能只会**变小**。
        *   但如果移动较矮的那个指针（例如 `i++`），宽度 `j-i` 仍然会减少。但是，我们**有机会**在 `i` 的新位置找到一个**比 `height[i]` 更高的柱子**。如果找到了更高的柱子，那么新的 `min(height[new_i], height[j])` 有可能**增加**，从而弥补宽度减少带来的损失，甚至**增加总面积**。
        *   **直白地说：** 那个矮的柱子是限制你面积的罪魁祸首。挪开它，你才有希望找到一个更好的组合。挪开高的那个，你不仅宽度减小，高度也可能保持不变或减小，纯粹是浪费机会。

**总结：双指针从两端向中间收缩，每步丢弃掉当前容器中高度较小的那根柱子，因为它是当前容器的容量限制，移动它才有机会找到更大的容器。这种贪心策略是有效的，因为它每次都排除了不可能成为最大面积短板的情况。**

### Way Of C

> source code

```c
int max_(int a, int b) {
    return a > b ? a : b;
}

int min_(int a, int b) {
    return a < b ? a : b;
}

int maxArea(int* height, int heightSize) {
    if (height == NULL || heightSize < 2) return 0;
    int res = 0, i = 0, j = heightSize - 1;
    while (i < j) {
        int current_area = min_(height[i], height[j]) * (j - i);
        res = max_(res, current_area);
        if (height[i] > height[j]) --j;
        else ++i;
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
    int maxArea(vector<int>& height) {
        int res = 0;
        for (int i = 0, j = height.size() - 1; i < j;) {
            res = max(res, min(height[i], height[j]) * (j - i));
            if (height[i] > height[j]) --j;
            else ++i;
        }
        return res;
    }
};
```
