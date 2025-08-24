https://www.acwing.com/problem/content/815/

```c++
#include <cstdio>

void print2D(int** a,int row,int col){
  for(int i=0;i<row;i++){
    for(int j=0;j<col;++j)
      printf("%d ", a[i][j]);
    printf("\n");
  }
}

int main(){
  int row,col;
  scanf("%d %d", &row, &col);
  
  int** a = new int*[row];
  for(int i=0;i<row;++i){
    a[i] = new int[col];
    for(int j=0;j<col;++j){
      scanf("%d", &a[i][j]);
    }
  }
  print2D(a,row,col);
  
  for(int i=0;i<row;++i)
    delete[] a[i];
  delete[] a;
  return 0;
}
```
