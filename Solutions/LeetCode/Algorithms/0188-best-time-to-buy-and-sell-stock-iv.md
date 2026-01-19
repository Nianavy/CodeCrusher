# Question

[best-time-to-buy-and-sell-stock-iv](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/)



# Answer

> solution

**核心思路：动态规划（DP）状态压缩**

1.  **无限交易处理**：若 $k \ge n/2$，则视为无限制交易，直接贪心累加所有上涨差价。

2.  **DP 状态定义**：
    *   `buy[j]`：持有 $j$ 次交易状态（买入）下的最大现金。
    *   `sell[j]`：完成 $j$ 次交易状态（不持股票）下的最大现金。

3.  **状态转移**（滚动数组，逆序遍历防状态污染）：
    *   `buy[j] = max(buy[j], sell[j-1] - price)` （买入时机：用上一次卖出的现金买入）
    *   `sell[j] = max(sell[j], buy[j] + price)` （卖出时机：用当前持有的股票卖出获利）

4.  **优化**：使用两个一维数组代替二维数组，空间复杂度优化至 $O(k)$。

### Way Of C

> source code

```c
static inline int max(int a, int b) {
    return a > b ? a : b;
}
int maxProfit(int k, int* prices, int pricesSize) {
    // 边界检查：输入为空或交易次数限制为0
    if (pricesSize <= 0 || k <= 0) {
        return 0;
    }
    // 特殊情况优化：
    // 如果 k 大于 pricesSize / 2，实际上相当于没有交易次数限制。
    // 因为 n 天最多只能完成 n/2 次完整的买卖操作（第 1 天买第 2 天卖...）。
    // 此时可以使用贪心算法，只要价格上涨就累加利润。
    if (k > pricesSize / 2) {
        int profit = 0;
        for (int i = 1; i < pricesSize; i++) {
            if (prices[i] > prices[i - 1]) {
                profit += prices[i] - prices[i - 1];
            }
        }
        return profit;
    }
    // 动态规划状态数组分配
    // buy[j]: 完成最多 j 次交易后，当前持有股票状态下的最大利润（或说“买入后的金钱”）。
    // sell[j]: 完成最多 j 次交易后，当前不持有股票状态下的最大利润（现金）。
    int* buy = (int*)malloc((k + 1) * sizeof(int));
    int* sell = (int*)malloc((k + 1) * sizeof(int));
    // 内存分配失败处理
    if (!buy || !sell) {
        free(buy);
        free(sell);
        return 0;
    }
    // 初始化 DP 数组
    // sell[0]: 0 次交易，不持有股票，利润为 0。
    // buy[0]: 0 次交易但持有股票（非合法状态下未定义），初始化为极小值INT_MIN/2防止溢出。
    for (int j = 0; j <= k; j++) {
        sell[j] = 0;
        buy[j] = INT_MIN / 2; 
    }
    // DP 核心递推循环
    for (int i = 0; i < pricesSize; i++) {
        int price = prices[i];
        
        // j 从 k 递减到 1：
        // 必须倒序，确保 sell[j-1] 和 buy[j-1] 使用的是上一轮（前一天）的状态值，
        // 而不是当前天刚更新后的值。
        for (int j = k; j >= 1; j--) {
            // 状态转移 1：买入
            // 当前持有股票的最大利润 = max(昨天持有股票 & 不卖, 昨天卖掉完成 j-1 次交易获得现金 & 今天买入)
            // 注意：这里更新的是 buy[j]，代表第 j 次交易的买入操作（准备进行第 j 次交易的后半段）
            buy[j] = max(buy[j], sell[j - 1] - price);
            
            // 状态转移 2：卖出
            // 当前持有现金的最大利润 = max(昨天持有现金不操作, 昨天买入了股票 & 今天卖出完成第 j 次交易)
            // buy[j] 已经被更新为包含“今天买入”的最优解，所以用它来计算卖出。
            sell[j] = max(sell[j], buy[j] + price);
        }
    }
    // 最终结果是持有 k 次交易上限完成后的最大现金，不持有股票
    int result = sell[k];
    // 内存清理：这是铁律，必须释放分配的资源
    free(buy);
    free(sell);
    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int maxProfit(int k, std::vector<int>& prices) {
        int n = prices.size();
        if (n <= 0 || k <= 0) return 0;
        
        // 特殊情况优化：
        // 如果 k 大于 n/2，实际上相当于没有交易次数限制。
        // 因为 n 天最多只能完成 n/2 次完整的买卖操作。
        // 此时可以使用贪心算法，只要价格上涨就累加利润。
        if (k > n / 2) {
            int profit = 0;
            for (int i = 1; i < n; i++) {
                if (prices[i] > prices[i - 1]) {
                    profit += prices[i] - prices[i - 1];
                }
            }
            return profit;
        }
        // 动态规划状态数组 (O(k) 空间)
        // buy[j]: 完成最多 j 次交易后，当前持有股票状态下的最大利润。
        // sell[j]: 完成最多 j 次交易后，当前不持有股票状态下的最大利润 (现金)。
        const int INF = 1e5; // 股票价格通常在此范围内
        std::vector<int> buy(k + 1, -INF);
        std::vector<int> sell(k + 1, 0);
        // 遍历每一天的价格
        for (int price : prices) {
            // j 从 k 递减到 1：
            // 必须倒序更新，确保 sell[j-1] 和 buy[j-1] 引用的是前一天的状态值。
            for (int j = k; j >= 1; j--) {
                // 状态转移 1：买入
                // 当前持有股票的最大利润 = max(昨天持有股票 & 不卖, 昨天卖掉完成 j-1 次交易获得现金 & 今天买入)
                buy[j] = std::max(buy[j], sell[j - 1] - price);
                
                // 状态转移 2：卖出
                // 当前持有现金的最大利润 = max(昨天持有现金不操作, 昨天买入了股票 & 今天卖出完成第 j 次交易)
                // buy[j] 已经被更新为包含“今天买入”的最优解，所以用它来计算卖出。
                // 这允许“今天买入并卖出”（通过 sell[j] = max(sell[j], buy[j] + price) 
                // 且 buy[j] = max(buy[j], sell[j-1] - price)），其效果等同于不做操作，
                // 但这在逻辑上是安全的，不会导致错误的超额利润。
                sell[j] = std::max(sell[j], buy[j] + price);
            }
        }
        // 最终结果是持有 k 次交易上限完成后的最大现金，不持有股票
        return sell[k];
    }
};
```
