# Question

[best-time-to-buy-and-sell-stock-iii](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/)



# Answer

> solution

用四个变量跟踪两次交易的状态，遍历一次价格数组，每次更新这四个状态，最后取最大的卖出利润。

### Way Of C

> source code

```c
int maxProfit(int* prices, int pricesSize) {
    int buy1 = INT_MIN, sell1 = 0;
    int buy2 = INT_MIN, sell2 = 0;
    
    for (int i = 0; i < pricesSize; i++) {
        int price = prices[i];
        
        // 更新第一次交易
        buy1 = buy1 > -price ? buy1 : -price;
        sell1 = sell1 > buy1 + price ? sell1 : buy1 + price;
        
        // 更新第二次交易
        buy2 = buy2 > sell1 - price ? buy2 : sell1 - price;
        sell2 = sell2 > buy2 + price ? sell2 : buy2 + price;
    }
    
    return sell1 > sell2 ? sell1 : sell2;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        // 四个状态：第一次买后，第一次卖后，第二次买后，第二次卖后
        int buy1 = INT_MIN, sell1 = 0;
        int buy2 = INT_MIN, sell2 = 0;
        
        for (int price : prices) {
            // 更新第一次交易的利润
            buy1 = max(buy1, -price);           // 第一次买入后的利润（负值）
            sell1 = max(sell1, buy1 + price);   // 第一次卖出后的利润
            
            // 基于第一次交易的利润进行第二次交易
            buy2 = max(buy2, sell1 - price);    // 第二次买入后的利润
            sell2 = max(sell2, buy2 + price);   // 第二次卖出后的利润
        }
        
        return max(sell1, sell2);
    }
};
```
