# Question

[single-number-ii](https://leetcode.cn/problems/single-number-ii/)



# Answer

> solution

针对“数组中除一个数字出现一次外，其余都出现三次”该问题的总结。这通过模拟三进制状态机来实现。
核心思路总结
目标：在 $O(N)$ 时间和 $O(1)$ 空间内找出唯一出现的数字。
基本原理：
如果我们要统计 32 位整数中每一位（bit）出现 1 的次数，且其他数字都出现三次，那么每一位的 1 的计数模 3 的余数，一定等于答案在该位上的值。
我们可以用两个比特位（ones 和 twos）的组合来表示一个三进制状态的计数器（00, 01, 10, 11... 但我们需要只用到前两个状态）：

00：表示该位出现了 0 次。
01：表示该位出现了 1 次。
10：表示该位出现了 2 次。
11：无意义（出现 3 次时会清零，回到 00）。

算法流程 (状态转移)
我们要维护两个变量 ones 和 twos。
对于数组中的每一个数字 x：


更新 ones：

此时 x 进来了。
逻辑：ones = (ones ^ x) & ~twos
含义：如果某一位在 twos 中已经是 1，说明它已经出现过 2 次了，加上这一次就是 3 次，必须清零，所以要用 & ~twos 屏蔽掉。异或操作 ^ 则是用来翻转状态（0 变 1 或 1 变 0）。



更新 twos：

此时 x 进来了。
逻辑：twos = (twos ^ x) & ~ones
含义：用更新后的 ones 来判断。如果某一位现在的 ones 是 1，说明它属于“出现 1 次”的状态，不能留在 twos 里，所以要用 & ~ones 排除掉。异或操作 ^ 将其推向“出现 2 次”的状态。



结果：

循环结束后，ones 保存的就是那个出现 1 次的数字（因为它只停留在“出现 1 次”的状态），twos 会回到 0。



总结
这就是一种硬件电路级的思维：不使用额外的数组或哈希表，而是直接操作位掩码，通过逻辑运算实现状态流转。这是解决此类问题的最优解。

### Way Of C

> source code

```c
int singleNumber(int* nums, int numsSize) {
    int ones = 0, twos = 0;
    for (int i = 0; i < numsSize; i++) {
        // 核心逻辑完全复制 C++ 版本，没有任何多余操作
        // 这种位运算在 C 语言里是标准操作，没有对象开销，性能最优。
        ones = (ones ^ nums[i]) & ~twos;
        twos = (twos ^ nums[i]) & ~ones;
    }
    return ones;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ones = 0, twos = 0;
        for (int x : nums) {
            // 更新 ones：原来的 ones 记录了出现过 1 次的位
            // 这次加上 x，如果某位在 twos 中已存在，说明之前出现过 2 次，现在变成 3 次，要在 ones 中清除 (mask by ~twos)
            // 同理，异或操作更新状态
            ones = (ones ^ x) & ~twos;
            // 更新 twos：原来的 twos 记录了出现过 2 次的位
            // 加上 x，如果某位在新的 ones 中存在，说明现在刚变成第 1 次，不能留在 twos 中
            twos = (twos ^ x) & ~ones;
        }
        return ones; // 那个唯一的数最终只留在 ones 里
    }
};
```
