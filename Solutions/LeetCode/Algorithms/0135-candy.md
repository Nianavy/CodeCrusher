# Question

[candy](https://leetcode.cn/problems/candy/)



# Answer

> solution

【解决思路】

**问题本质**
为每个孩子分配糖果，满足：
1. 相邻孩子中，**更高**评分者获得**更多**糖果
2. 最小化总糖果数

**数学洞察**
约束等价于两个独立方向：
- **正向**（从左到右）：若 `rating[i] > rating[i-1]`，则 `candy[i] > candy[i-1]`
- **反向**（从右到左）：若 `rating[i] > rating[i+1]`，则 `candy[i] > candy[i+1]`

**算法核心（两次遍历）**
```
初始化：所有孩子1颗糖

正向遍历：
for i = 1 to n-1:
    if rating[i] > rating[i-1]:
        candy[i] = candy[i-1] + 1
    // 否则保持1

反向遍历：
for i = n-2 downto 0:
    if rating[i] > rating[i+1] && candy[i] <= candy[i+1]:
        candy[i] = candy[i+1] + 1
    // 必须取max，确保不破坏已满足的正向约束

总和：汇总所有candy[i]
```

**关键机制**
- **正向遍历**：保证所有"递增"约束
- **反向遍历**：保证所有"递减"约束，同时通过`max`不破坏已满足的递增约束
- **合并**：两次遍历后，所有局部约束同时满足

**为什么有效**
- 单次遍历只能满足一个方向
- 两次遍历分别处理两个方向，取max合并结果
- 初始值1保证最小性

**复杂度**
- **时间**：O(n) - 两遍扫描
- **空间**：O(n) - 存储糖果分配

**边界情况**
- 空数组：返回0
- 相等评分：candy不变
- 单元素：返回1

### Way Of C

> source code

```c
int candy(int* ratings, int ratingsSize) {
    if (ratingsSize == 0) return 0;
    
    int* candies = (int*)malloc(ratingsSize * sizeof(int));
    if (!candies) return 0;  // 安全检查
    
    // 初始化
    for (int i = 0; i < ratingsSize; ++i) {
        candies[i] = 1;
    }
    
    // 正向遍历
    for (int i = 1; i < ratingsSize; ++i) {
        if (ratings[i] > ratings[i - 1]) {
            candies[i] = candies[i - 1] + 1;
        }
    }
    
    // 反向遍历并求和
    int sum = candies[ratingsSize - 1];
    for (int i = ratingsSize - 2; i >= 0; --i) {
        if (ratings[i] > ratings[i + 1]) {
            candies[i] = candies[i] > candies[i + 1] + 1 ? 
                         candies[i] : candies[i + 1] + 1;
        }
        sum += candies[i];
    }
    
    free(candies);  // 释放内存
    return sum;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int candy(vector<int>& ratings) {
        int n = ratings.size();
        if (n == 0) return 0;
        
        vector<int> candies(n, 1);
        
        // 正向：保证右邻居更大
        for (int i = 1; i < n; ++i) {
            if (ratings[i] > ratings[i - 1]) {
                candies[i] = candies[i - 1] + 1;
            }
        }
        
        // 反向：保证左邻居更大，同时取max
        int sum = candies[n - 1];
        for (int i = n - 2; i >= 0; --i) {
            if (ratings[i] > ratings[i + 1]) {
                candies[i] = max(candies[i], candies[i + 1] + 1);
            }
            sum += candies[i];
        }
        
        return sum;
    }
};
```
