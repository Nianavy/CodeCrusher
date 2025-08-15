https://www.acwing.com/problem/content/3706/

### Think
```
Use a stack to validate bracket sequences.
Push opening brackets as negative values; matching closing brackets have positive values.
Check for correct matching and proper nesting order by comparing absolute values.
Return true if all matched and stack is empty.
```

### Ways
```C++
#include <iostream>
#include <unordered_map>
#include <stack>
#include <algorithm>

std::unordered_map<char, int> mp;

bool check(std::string str) {
  std::stack<int> stk;
  for (auto c: str) {
    int t = mp[c];
    if (t > 0) {
      if (stk.empty() || stk.top() != -t) return false;
      stk.pop();
    }
    else {
      if (stk.size() && abs(stk.top()) < abs(t)) return false;
      stk.push(t);
    }
  }
  return stk.empty();
}

int main() {
  mp['{'] = -4, mp['}'] = 4;
  mp['['] = -3, mp[']'] = 3;
  mp['('] = -2, mp[')'] = 2;
  mp['<'] = -1, mp['>'] = 1;
  
  int T;
  std::cin >> T;
  
  while (T--) {
    std::string str;
    std::cin >> str;
    
    if (check(str)) std::cout << "YES" << std::endl;
    else std::cout << "NO" << std::endl;
  }
  return 0;
}
```
