# Question

[best-time-to-buy-and-sell-stock](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)



# Answer

> solution

遍历一次、顺手记录“截至目前的最小值”即可得到最大利润；
核心姿势只有一句：max_profit = max(max_profit, 当前价 - 当前最小价)，然后顺手更新当前最小价。

### Way Of C

> source code

```c
int maxProfit(int *prices, int pricesSize) {
    int res = 0, minp = INT_MAX;
    for (int i = 0; i < pricesSize; ++i) {
        res = (prices[i] - minp) > res ? prices[i] - minp : res;
        minp = prices[i] < minp ? prices[i] : minp;
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
    int maxProfit(vector<int>& prices) {
        int res = 0, minp = INT_MAX;
        for (int p : prices) {
            res = max(res, p - minp);
            minp = min(minp, p);
        }
        return res;
    }
};
```
