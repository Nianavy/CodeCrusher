https://www.acwing.com/problem/content/672/

```c++
#include <cstdio>
#include <cstring>

char str_x[101], str_y[101], str_z[101];

int main() {
  scanf("%s %s %s", str_x, str_y, str_z);
  if (strcmp(str_x, "invertebrado") == 0) {
    if (strcmp(str_y, "inseto") == 0) {
      if (strcmp(str_z, "hematofago") == 0) puts("pulga");
      else puts("lagarta");
    }
    else {
      if (strcmp(str_z, "hematofago") == 0) puts("sanguessuga");
      else puts("minhoca");
    }
  }
  else {
    if (strcmp(str_y, "ave") == 0) {
      if (strcmp(str_z, "carnivoro") == 0) puts("aguia");
      else puts("pomba");
    }
    else {
      if (strcmp(str_z, "onivoro") == 0) puts("homem");
      else puts("vaca");
    }
  }
  return 0;
}
```
