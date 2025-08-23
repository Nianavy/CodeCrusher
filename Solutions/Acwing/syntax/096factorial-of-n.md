https://www.acwing.com/problem/content/806/

```c++
#include<iostream>
using namespace std;

int fact(int n){
  int ans = 1;
  for(int i=1;i<=n;++i)ans *= i;
  return ans;
}

int main(){
  int n;
  scanf("%d",&n);
  cout<<fact(n)<<endl;
  return 0;
}
```
