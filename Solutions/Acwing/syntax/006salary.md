https://www.acwing.com/problem/content/611/

```c++
#include <cstdio>

int no, worktime;
double salary;

int main() {
  scanf("%d %d %lf", &no, &worktime, &salary);
  printf("NUMBER = %d\nSALARY = U$ %.2lf\n", no, salary * worktime);
  return 0;
}
```
