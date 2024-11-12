https://leetcode.cn/problems/merge-sorted-array

### Think:
```txt
理解问题：

nums1 和 nums2 都是已排序的数组。
nums1 的长度为 m + n，其中前 m 个元素是已排序的，后 n 个元素是空位。
nums2 的长度为 n，所有元素都是已排序的。
目标是将 nums2 的元素合并到 nums1 中，使 nums1 保持有序。
选择合并策略：

由于两个数组都是已排序的，可以使用双指针法从后向前合并。
从后向前合并的好处是不需要额外的空间，直接在 nums1 中操作即可。
初始化指针：

p1 指向 nums1 的最后一个有效元素，即 m - 1。
p2 指向 nums2 的最后一个有效元素，即 n - 1。
tail 指向 nums1 的最后一个位置，即 m + n - 1。
合并过程：

使用一个 while 循环，条件是 p1 或 p2 任一指针不小于 0。
在每次循环中，比较 nums1[p1] 和 nums2[p2] 的值，选择较大的值放到 nums1[tail] 位置。
更新指针 p1 或 p2，以及 tail。
处理边界情况：

如果 p1 已经小于 0，说明 nums1 的有效部分已经全部处理完毕，此时直接将 nums2 的剩余部分复制到 nums1 中。
如果 p2 已经小于 0，说明 nums2 的有效部分已经全部处理完毕，此时 nums1 的剩余部分已经是有序的，不需要再处理。
```
```

### Ways
By C++:
```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int p1 = m - 1, p2 = n - 1;
        int tail = m + n - 1;
        int cur;
        while (p1 >= 0 || p2 >= 0) {
            if (p1 == -1) {
                cur = nums2[p2--];
            } else if (p2 == -1) {
                cur = nums1[p1--];
            } else if (nums1[p1] > nums2[p2]) {
                cur = nums1[p1--];
            } else {
                cur = nums2[p2--];
            }
            nums1[tail--] = cur;
        }
    }
};
```
