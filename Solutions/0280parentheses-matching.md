https://www.acwing.com/problem/content/3696/

### Think
```
Push on open, pop on close;
valid if stack is empty in the end.
```

### Ways
```C++
#include <iostream>
#include <stack>
#include <unordered_map>
#include <string>

int main() {
    std::unordered_map<char, int> mp = {
        {'<', -1}, {'>', 1},
        {'(', -2}, {')', 2},
        {'[', -3}, {']', 3},
        {'{', -4}, {'}', 4},
    };

    std::string str;
    std::cin >> str;

    std::stack<int> stk;
    bool res = true;

    for (char c : str) {
        int t = mp[c];
        if (t < 0) {
            stk.push(t);
        } else {
            if (stk.empty() || stk.top() != -t) {
                res = false;
                break;
            }
            stk.pop();
        }
    }

    if (!stk.empty()) {
        res = false;
    }

    if (res) std::cout << "yes" << std::endl;
    else std::cout << "no" << std::endl;

    return 0;
}
```
