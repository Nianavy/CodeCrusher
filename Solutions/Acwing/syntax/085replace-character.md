https://www.acwing.com/problem/content/771/

```c++
#include<iostream>
using namespace std;
int main(){
  string i;
  char o;
  cin>>i>>o;
  for(auto &iter : i){
    if(iter == o)
      iter = '#';
  }
  cout<<i<<endl;
  return 0;
}
```
