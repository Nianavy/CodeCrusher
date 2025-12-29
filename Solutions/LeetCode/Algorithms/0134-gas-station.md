# Question

[gas-station](https://leetcode.cn/problems/gas-station/)



# Answer

> solution

【解决思路】

**问题本质**
在环形加油站序列中找唯一起点，使从该点出发的任意前缀油量非负。

**核心数学洞察**
1. **整体约束**：总gas ≥ 总cost，否则无解
2. **局部跳跃性**：若从i无法到达j，则i到j之间任一位置都不能做起点
3. **唯一性**：满足以上条件，有且仅有一个起点

**算法流程**
```
total = 0     // 全局总油量，判断可行性
tank = 0      // 当前区间油量，判断局部可行性
start = 0     // 候选起点

for i = 0 to n-1:
    diff = gas[i] - cost[i]
  
    total += diff
    tank += diff
  
    if tank < 0:        // 当前区间无法到达i+1
        tank = 0        // 放弃当前区间
        start = i + 1   // 下一个位置是新起点候选
```

**最终判断**
- 若 `total < 0` → 无解，返回-1
- 否则返回 `start`

**时间复杂度**：O(n)
**空间复杂度**：O(1)

**为什么有效**
- 若存在解，最后一个失败区间之后的start必为解
- 总油量不足直接排除所有可能性
- 单遍扫描覆盖所有起点可能性

### Way Of C

> source code

```c
int canCompleteCircuit(int* gas, int gasSize, int* cost, int costSize) {
    int total = 0, tank = 0, start = 0;
    for (int i = 0; i < gasSize; i++) {
        int diff = gas[i] - cost[i];
        total += diff;
        tank += diff;
        if (tank < 0) {
            tank = 0;
            start = i + 1;
        }
    }
    return total < 0 ? -1 : start;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int total = 0, tank = 0, start = 0;
        for (int i = 0; i < gas.size(); i++) {
            int diff = gas[i] - cost[i];
            total += diff;
            tank += diff;
            if (tank < 0) {         // 无法从start到达i+1
                tank = 0;           // 重置当前油量
                start = i + 1;      // 下一个位置可能是起点
            }
        }
        return total < 0 ? -1 : start;
    }
};
```
