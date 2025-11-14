# Question

[search-in-rotated-sorted-array-ii](https://leetcode.cn/problems/search-in-rotated-sorted-array-ii/)



# Answer

> solution

搜索旋转排序数组 II

### 1. 核心挑战：重复元素导致的模糊性

在标准的旋转数组搜索中，我们通过比较 `nums[low]` 和 `nums[mid]` 来确定哪一半是有序的。

*   **问题**：当 `nums[low] == nums[mid] == nums[high]` 时，我们无法通过这三个端点判断旋转点位于左侧还是右侧。任何一侧都可能是有序的，也可能是完全由重复值构成的。
*   **解决方案**：遇到这种情况，我们只能**收缩边界**：`low++` 和 `high--`。虽然这在最坏情况下（例如，数组全部是相同的数字，除了一个目标值）会导致 $O(N)$ 的时间复杂度，但在 $O(1)$ 额外空间下，这是必要的代价。

### 2. 标准的二分查找流程

在排除了模糊情况之后，搜索流程回归到标准的逻辑判断：

#### 步骤 I：处理模糊区域（O(N) 退化）

$$\text{如果 } nums[low] = nums[mid] = nums[high] \text{，则 } low \leftarrow low + 1, high \leftarrow high - 1 \text{。}$$

#### 步骤 II：确定有序区间

如果不是模糊情况，那么 $\text{nums}[low]$ 和 $\text{nums}[mid]$ 必然有一个不等式，使得至少一半是有序的。

**A. 左侧 $[low, mid]$ 有序** ($\text{nums}[low] \le \text{nums}[mid]$)

1.  **检查目标**：判断 $target$ 是否落在左侧有序区间内：
    $$\text{如果 } nums[low] \le target \le nums[mid] \text{，则 } high \leftarrow mid - 1$$
2.  **否则**：目标在右侧（无序）区间，搜索范围缩小到右侧：
    $$low \leftarrow mid + 1$$

**B. 右侧 $[mid, high]$ 有序** ($\text{nums}[low] > \text{nums}[mid]$ 成立时)

1.  **检查目标**：判断 $target$ 是否落在右侧有序区间内：
    $$\text{如果 } nums[mid] \le target \le nums[high] \text{，则 } low \leftarrow mid + 1$$
2.  **否则**：目标在左侧（无序）区间，搜索范围缩小到左侧：
    $$high \leftarrow mid - 1$$

通过这种流程，我们在每次迭代中都能排除至少一半（非模糊情况），或排除至少两个元素（模糊情况），从而保证了算法的正确性和尽可能高的效率。

### Way Of C

> source code

```c
/**
 * 搜索旋转排序数组 II (允许重复元素)
 * O(log N) 平均时间，最坏 O(N)
 */
bool search(int* nums, int numsSize, int target)
{
    if (numsSize == 0) {
        return false;
    }

    int low = 0;
    int high = numsSize - 1;

    while (low <= high) {
        // 使用 low + (high - low) / 2 防止整数溢出
        int mid = low + (high - low) / 2;
        
        if (nums[mid] == target) {
            return true;
        }

        // 核心难点：处理重复元素导致的模糊区域
        if (nums[low] == nums[mid] && nums[mid] == nums[high]) {
            // 无法判断哪一侧有序，收缩边界。这是 O(N) 退化的原因。
            low++;
            high--;
            continue; // 跳过当前 mid 的后续判断
        } 
        
        // 1. 判断左侧 [low, mid] 是否有序
        if (nums[low] <= nums[mid]) {
            // 左侧有序
            
            // 目标是否在左侧有序区间内?
            if (nums[low] <= target && target < nums[mid]) {
                high = mid - 1; // 目标在左侧，收缩右侧
            } else {
                low = mid + 1; // 目标在右侧，收缩左侧
            }
        } 
        // 2. 左侧无序，则右侧 [mid, high] 必定有序
        else {
            // 右侧有序
            
            // 目标是否在右侧有序区间内?
            if (nums[mid] < target && target <= nums[high]) {
                low = mid + 1; // 目标在右侧，收缩左侧
            } else {
                high = mid - 1; // 目标在左侧，收缩右侧
            }
        }
    }

    return false;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    bool search(vector<int>& nums, int target) {
        int low = 0;
        int high = nums.size() - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            if (nums[mid] == target) {
                return true;
            }

            // **核心难点：处理重复元素导致的模糊区域**
            if (nums[low] == nums[mid] && nums[mid] == nums[high]) {
                // 无法确定哪一侧有序，只能收缩边界，最坏 O(N)
                low++;
                high--;
                continue; // 跳过当前 mid 的后续判断
            }
            
            // 1. 判断左侧 [low, mid] 是否有序
            if (nums[low] <= nums[mid]) {
                // 左侧有序
                
                // 目标是否在左侧有序区间内?
                if (nums[low] <= target && target < nums[mid]) {
                    high = mid - 1; // 搜索左侧
                } else {
                    low = mid + 1; // 搜索右侧 (无序或目标更大)
                }
            } 
            // 2. 左侧无序，则右侧 [mid, high] 必定有序
            else {
                // 右侧有序
                
                // 目标是否在右侧有序区间内?
                if (nums[mid] < target && target <= nums[high]) {
                    low = mid + 1; // 搜索右侧
                } else {
                    high = mid - 1; // 搜索左侧 (无序或目标更小)
                }
            }
        }

        return false;
    }
};
```
