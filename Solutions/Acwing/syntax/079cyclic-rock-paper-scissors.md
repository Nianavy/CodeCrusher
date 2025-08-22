https://www.acwing.com/problem/content/765/

```c++
#include <cstdio>

int game;
char one[10], two[10];

int main() {
    scanf("%d", &game);
    while(game--){
        scanf("%s %s", one, two);

        if(one[0]=='H'&&two[0]=='H') printf("Tie\n");
        if(one[0]=='H'&&two[0]=='G') printf("Player1\n");
        if(one[0]=='H'&&two[0]=='B') printf("Player2\n");            

        if(one[0]=='B'&&two[0]=='B') printf("Tie\n");
        if(one[0]=='B'&&two[0]=='H') printf("Player1\n");
        if(one[0]=='B'&&two[0]=='G') printf("Player2\n");

        if(one[0]=='G'&&two[0]=='G') printf("Tie\n");
        if(one[0]=='G'&&two[0]=='B') printf("Player1\n");
        if(one[0]=='G'&&two[0]=='H') printf("Player2\n");
    }
    return 0;
}
```
