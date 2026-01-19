# Question

[two-sum-ii-input-array-is-sorted](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)



# Answer

> solution

1.  **问题**：已排序数组，找两个和为目标值的索引。时间复杂度要求 O(n)。
2.  **方法**：双指针法。指针 i 从头开始，j 从尾开始。
3.  **逻辑**：循环比较 `numbers[i] + numbers[j]` 与 `target`。
    *   和 < 目标 → i 右移。
    *   和 > 目标 → j 左移。
    *   和 == 目标 → 找到，返回 `i+1` 和 `j+1`。
4.  **细节**：
    *   C 语言 `sizeof *indexes` 更安全。
    *   C++ 语言直接返回 `{i+1, j+1}` 最简洁。
5.  **核心**：算法 O(n) 是最优解，实现追求最小化复杂性和最直接表达。

### Way Of C

> source code

```c
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* twoSum(int* numbers, int numbersSize, int target, int* returnSize) {
    *returnSize = 0;
    int i = 0, j = numbersSize - 1;
    while (i < j) {
        int sum = numbers[i] + numbers[j];
        if (sum < target) {
            i++;
        } else if (sum > target) {
            j--;
        } else {
            *returnSize = 2;
            // 使用 sizeof(*indexes) 更安全
            int* indexes = malloc(2 * sizeof(*indexes));
            indexes[0] = i + 1;
            indexes[1] = j + 1;
            return indexes;
        }
    }
    return NULL;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int i = 0, j = numbers.size() - 1;
        while (i < j) {
            int sum = numbers[i] + numbers[j];
            if (sum < target) {
                i++;
            } else if (sum > target) {
                j--;
            } else {
                // 直接构造并返回，消除了不必要的临时对象和 push_back
                return {i + 1, j + 1};
            }
        }
        // 理论上不会执行到这里，但为了编译器警告和代码完整性保留
        return {};
    }
};
```
