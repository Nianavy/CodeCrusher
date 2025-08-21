https://www.acwing.com/problem/content/618/

```c++
#include <cstdio>
#include <cmath>

double distance(double x1,double x2,double y1,double y2){
    double ans = (x2-x1)*(x2-x1) + (y2-y1)*(y2-y1);
    ans = sqrt(ans);
    return ans;
}

int main(){
    double x1,y1;
    scanf("%lf %lf",&x1,&y1);
    double x2,y2;
    scanf("%lf %lf",&x2,&y2);
    double ans = distance(x1,x2,y1,y2);
    printf("%.4lf\n",ans);
    return 0;
}
```
