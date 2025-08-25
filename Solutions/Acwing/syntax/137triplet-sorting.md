https://www.acwing.com/problem/content/864/

```c++
#include <iostream>
#include <algorithm>
#include <iomanip>
#include <vector>

using namespace std;

struct Triple {
  int x;
  double y;
  string z;
};

bool compare(const Triple &a, const Triple &b) { return a.x < b.x; }

int main() {
  int N;
  cin >> N;
  vector<Triple> triples(N);
  for (int i = 0; i < N; ++i) cin >> triples[i].x >> triples[i].y >> triples[i].z;
  sort(triples.begin(), triples.end(), compare);
  for (const auto &triple: triples)
    cout << triple.x << " " << fixed << setprecision(2) << triple.y << " " << triple.z << endl;
  return 0;
}
```
