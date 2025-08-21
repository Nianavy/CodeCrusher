https://www.acwing.com/problem/content/616/

```c++
#include <cstdio>
#include <cmath>

int max(int a,int b){
  int ab = (a+b+abs(a-b))/2;
  return ab;
}

int a, b, c;

int main() {
  scanf("%d %d %d", &a, &b, &c);
  printf("%d eh o maior\n", max(max(a, b), c));
  return 0;
}
```
