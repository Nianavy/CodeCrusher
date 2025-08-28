# Question

[median-of-two-sorted-arrays](https://leetcode.cn/problems/median-of-two-sorted-arrays/)



# Answer

> solution

1.  **问题转化：查找第 K 小元素 (Find Kth Element)。**
    *   **思想：** 无论总长度 `(nums1Size + nums2Size)` 是奇数还是偶数，中位数都可以通过找到一个或两个“第 K 小的元素”来确定。
        *   如果总长度 `L` 是奇数，中位数就是第 `(L / 2 + 1)` 小的元素。
        *   如果总长度 `L` 是偶数，中位数是第 `(L / 2)` 小和第 `(L / 2 + 1)` 小的元素的平均值。
    *   **关键：** 这种转化将“查找中位数”这个复杂问题，简化为更通用的“在两个有序数组中查找第 K 小元素”这一子问题。

2.  **二分查找的核心递归策略：**
    *   **思想：** `findKthElement` 函数通过递归调用，每次都能安全地排除掉一定数量的元素，从而将搜索范围 (`k` 的值) 减半。
    *   **如何排除：**
        1.  **确定要跳过的数量：** 在当前 `k` 值下，我们尝试从 `nums1` 和 `nums2` 各自跳过 `k/2` 个元素（或者更少，如果数组不够长）。例如，在 `nums1` 中跳过 `pa` 个，在 `nums2` 中跳过 `pb` 个，且 `pa + pb` 大致等于 `k`。
        2.  **比较跳过后的元素：** 比较 `nums1` 中第 `pa` 个元素 (`nums1[pa-1]`) 和 `nums2` 中第 `pb` 个元素 (`nums2[pb-1]`)。
        3.  **做出决策并递归：**
            *   如果 `nums1[pa-1] < nums2[pb-1]`，这意味着第 `k` 小的元素不可能在 `nums1` 的前 `pa` 个元素中，因为它们都太小了。所以，我们可以安全地排除 `nums1` 的前 `pa` 个元素，然后递归地在剩余的数组中查找第 `k - pa` 小的元素。
            *   如果 `nums1[pa-1] > nums2[pb-1]`，同理，第 `k` 小的元素不可能在 `nums2` 的前 `pb` 个元素中。排除 `nums2` 的前 `pb` 个元素，递归查找第 `k - pb` 小的元素。
            *   如果 `nums1[pa-1] == nums2[pb-1]`，那么这个相等的值就是第 `k` 小的元素。
    *   **效率：** 每次递归，`k` 的值都会大致减半，因此递归的深度是对数级的，时间复杂度为 O(log(min(M,N)))。

3.  **鲁棒的边界条件和优化：**
    *   **确保短数组在前：** 这是一个非常重要的优化(`if (size1 > size2) return findKthElement(ptr2, size2, ptr1, size1, k);` 或 C++ 版本中的 `if (nums1.size() - i > nums2.size() - j)` )。它保证了在计算 `pa` (即 `k/2` 的跳跃步数) 时，不会因为 `nums1` 过短而导致越界，并且简化了逻辑。
    *   **基本情况处理：**
        *   当一个数组已经完全被排除时 (`size1 == 0` 或 `nums1.size() == i`)，直接从另一个数组中取第 `k` 个元素。
        *   当 `k == 1` 时，直接返回两个数组当前首元素中的最小值。
    *   **这些边界条件是递归正确终止的关键。**

### Way Of C

> think

    *   直接使用 `int* ptr` 和 `int size` 来表示子数组，`ptr + pa` 这种裸指针算术是其核心。这提供了最直接的内存访问，没有 `std::vector` 的额外抽象层，所以在某些极端性能场景下可能略快一点点，但代价是程序员需要更小心地处理指针和数组边界。

> source code

```c
int findKthElement(int *ptr1, int size1, int *ptr2, int size2, int k) {
    if (size1 > size2) return findKthElement(ptr2, size2, ptr1, size1, k);
    if (size1 == 0) return ptr2[k - 1];
    if (k == 1) return (size1 == 0) ? ptr2[0] : ((size2 == 0) ? ptr1[0] : (ptr1[0] < ptr2[0] ? ptr1[0] : ptr2[0]));
    if (k == 1) return ptr1[0] < ptr2[0] ? ptr1[0] : ptr2[0];
    int pa = k / 2 < size1 ? k / 2 : size1;
    int pb = k - pa;
    if (ptr1[pa - 1] < ptr2[pb - 1]) return findKthElement(ptr1 + pa, size1 - pa, ptr2, size2, k - pa);
    else if (ptr1[pa - 1] > ptr2[pb - 1]) return findKthElement(ptr1, size1, ptr2 + pb, size2 - pb, k - pb);
    else return ptr1[pa - 1];
}

double findMedianSortedArrays(int* nums1, int nums1Size, int* nums2, int nums2Size) {
    int total_len = nums1Size + nums2Size;
    if (total_len % 2 == 1) return (double)findKthElement(nums1, nums1Size, nums2, nums2Size, total_len / 2 + 1);
    else {
        int left_median = findKthElement(nums1, nums1Size, nums2, nums2Size, total_len / 2);
        int right_median = findKthElement(nums1, nums1Size, nums2, nums2Size, total_len / 2 + 1);
        return ((double)left_median + right_median) / 2.0;
    }
}
```

---


### Way Of C++

> think

    *   利用 `std::vector` 的封装，通过传递 `vector` 引用和索引 `i, j` 来表示子数组的范围。这比 C 语言的裸指针操作更安全，更不容易出错。
    *   使用 `std::min` 和 `std::size_t` 等 C++ 特性，代码更现代，可读性更好。


> source code

```c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int tot= nums1.size() + nums2.size();
        if (tot % 2 == 0) {
            int left = find(nums1, 0, nums2, 0, tot / 2);
            int right = find(nums1, 0, nums2, 0, tot / 2 + 1);
            return (left + right) / 2.0;
        }
        else return find(nums1, 0, nums2, 0, tot / 2 + 1);
    }

    int find(vector<int> &nums1, int i, vector<int> &nums2, int j, int k) {
        if (nums1.size() - i > nums2.size() - j) return find(nums2, j, nums1, i, k);
        if (k == 1) {
            if (nums1.size() == i) return nums2[j];
            else return min(nums1[i], nums2[j]);
        }
        if (nums1.size() == i) return nums2[j + k - 1];
        int si = min((int)nums1.size(), i + k / 2), sj = j + k - k / 2;
        if (nums1[si - 1] > nums2[sj - 1]) return find(nums1, i, nums2, sj, k - (sj - j));
        else return find(nums1, si, nums2, j, k - (si - i));
    }
};
```
