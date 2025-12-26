# Question

[best-time-to-buy-and-sell-stock-ii](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)



# Answer

> solution

遍历价格数组，从第二天开始。如果今天价格比昨天高，就把差价加入总利润。时间复杂度O(n)，空间复杂度O(1)。

### Way Of C

> source code

```c
int maxProfit(int* prices, int pricesSize) {
    int total_profit = 0;
    
    for (int i = 1; i < pricesSize; i++) {
        int daily_change = prices[i] - prices[i - 1];
        if (daily_change > 0) {
            total_profit += daily_change;
        }
    }
    
    return total_profit;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int total_profit = 0;
        
        for (int i = 1; i < prices.size(); i++) {
            total_profit += max(0, prices[i] - prices[i - 1]);
        }
        
        return total_profit;
    }
};
```
