https://www.acwing.com/problem/content/792/

### Think
```
binary-search
```

### Ways
By C++
```C++
#include <cstdio> 

double cubeRoot(double x, double l = -100, double r = 100, double limit = 1e-8) {
    while (r - l > limit) {
        double mid = l + (r - l) / 2;
        if (mid * mid * mid >= x) {
            r = mid;
        } else {
            l = mid;
        }
    }
    return l;
}

int main() {
    double x;
    scanf("%lf", &x);
    double result = cubeRoot(x);
    printf("%.6lf\n", result);
    return 0;
}
```
