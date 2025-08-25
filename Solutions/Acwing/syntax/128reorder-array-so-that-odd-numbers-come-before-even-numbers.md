https://www.acwing.com/problem/content/30/

```c++
class Solution {
public:
    void reOrderArray(vector<int> &array) {
      int left = 0;
      int right = array.size() - 1;
      while (left < right) {
        while (left < right && array[left] % 2 == 1) ++left;
        while (left < right && array[right] % 2 == 0) --right;
        if (left < right) swap(array[left], array[right]);
      }
    }
};
```
