https://www.acwing.com/problem/content/3721/

### Think
```
Enumerate all possible combinations and calculate and optimize the combinations that meet the conditions.
```

### Ways
```C++
#include <cstdio>      // For scanf, printf, puts
#include <cstring>     // For string manipulation if needed, though std::string is used
#include <algorithm>   // For std::min, std::max
#include <string>      // For std::string, std::stoll
#include <vector>      // Not directly used, but common for competitive programming

using namespace std;

typedef long long LL;

int main()
{
    int n, m;
    char s_cstr[1010]; // 使用 C 风格字符数组代替 std::string for input, max size 1010
    string s;          // 但仍然使用 std::string 来处理 substr 和 stoll

    // 读取 n, m, 和字符串 s
    scanf("%d%d", &n, &m);
    scanf("%s", s_cstr); // 读取 C 风格字符串
    s = s_cstr;         // 将 C 风格字符串转换为 std::string

    LL res = 0;

    // n-1 是字符串 s 中可能插入运算符的“间隔”数量
    int cnt = n - 1;
    // 遍历所有可能的组合，共有 2^(n-1) 种
    // i 代表了每个间隔处是否插入运算符（1）或拼接数字（0）的二进制状态
    for (int i = 0; i < (1 << cnt); i ++ )
    {
        int t = 0; // 计数器，用于统计当前组合中插入的运算符数量
        // 统计当前组合 i 中设置的位（即运算符数量）
        for (int j = 0; j < cnt; j ++ )
            // 检查第 j 位是否为 1
            if ((i >> j) & 1)
                t++;

        // 如果当前组合的运算符数量等于 m，则进行计算
        if (t == m)
        {
            LL p = 1; // 用于计算当前组合表达式的值
            string current_num_str; // 用于存储连续数字组成的字符串
            current_num_str += s[0]; // 从字符串 s 的第一个字符开始

            // 遍历字符串 s，根据二进制状态 i 来分割数字并进行乘法运算
            for (int j = 0; j < cnt; j ++ )
            {
                // 检查第 j 位是否为 1 (表示在 s[j+1] 处插入运算符)
                if ((i >> j) & 1)
                {
                    // 如果是 1，则将前面累积的数字字符串转换为 LL，并乘以当前的 p
                    p *= stoll(current_num_str);
                    // 清空当前数字字符串，准备接收下一个数字
                    current_num_str = "";
                }
                // 将当前字符 s[j+1] 追加到当前的数字字符串
                current_num_str += s[j + 1];
            }
            // 处理最后一个数字（或最后一个累积的数字字符串）
            p *= stoll(current_num_str);

            // 更新结果 res 为当前计算出的 p 和之前最大值的较大者
            res = max(res, p);
        }
    }

    // 输出最终结果
    printf("%lld\n", res); // 使用 %lld 来打印 long long 类型

    return 0;
}
```
