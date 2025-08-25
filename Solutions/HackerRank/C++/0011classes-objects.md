https://www.hackerrank.com/challenges/classes-objects

```txt
class and objects.
```

```c++
#include <cmath>
#include <cstdio>
#include <vector>
#include <iostream>
#include <algorithm>
#include <numeric>
using namespace std;

class Student {
private:
    vector<int> scores_;
public:
    void input() {
        for (int i = 0; i < 5; ++i) {
            int score_item;
            cin >> score_item;
            scores_.push_back(score_item);
        }
    }
    
    int calculateTotalScore() {
        int total_score = 0;
        for (int score: scores_) total_score += score;;
        return total_score;
    }
};

int main() {
    /* Enter your code here. Read input from STDIN. Print output to STDOUT */
     int n;
     cin >> n;
     vector<Student> students(n);
     int kristen_total_score = 0;
     for (int i = 0; i < n; ++i) {
        students[i].input();
        if (!i) kristen_total_score = students[i].calculateTotalScore();
     }
     int higher_scores_cnt = 0;
     for (int i = 1; i < n; ++i) {
        if (students[i].calculateTotalScore() > kristen_total_score)
            higher_scores_cnt++;
     }
     cout << higher_scores_cnt << endl;
    return 0;
}
```
