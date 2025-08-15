https://leetcode.cn/problems/spiral-matrix-ii

### Think
```TXT
模拟螺旋矩阵的生成过程，通过控制边界以及移动方向来填充矩阵。

初始化:

matrix: 创建一个 n x n 的矩阵，用于存储结果。
curNum: 当前填充的数字，初始值为 1。
directions: 定义四个方向的移动向量，分别表示右、下、左、上。
directionIndex: 当前移动方向的索引，初始值为 0（向右）。
left, right, top, bottom: 定义矩阵的边界，初始值分别为 0, n-1, 0, n-1。
循环填充:

while (curNum <= maxNum): 循环直到填充完所有数字。
matrix[row][column] = curNum++: 填充当前位置的数字，并将 curNum 加 1。
边界控制和方向切换:

使用四个 if 语句判断是否到达边界：

column == right: 到达右边界。
row == bottom: 到达下边界。
column == left: 到达左边界。
row == top: 到达上边界。
如果到达边界，则更新对应的边界值（例如，到达右边界后，top++，缩小上边界），并通过 directionIndex = (directionIndex + 1) % 4 切换到下一个方向。

移动:

row += directions[directionIndex][0]: 根据当前方向更新行坐标。
column += directions[directionIndex][1]: 根据当前方向更新列坐标。
```

### Ways
By C++
```C++
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        int maxNum = n * n;
        int curNum = 1;
        vector<vector<int>> matrix(n, vector<int>(n));
        int row = 0, column = 0;
        vector<vector<int>> directions = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        int directionsIndex = 0;
        while (curNum <= maxNum) {
            matrix[row][column] = curNum;
            curNum++;
            int nextRow = row + directions[directionsIndex][0], nextColumn = column + directions[directionsIndex][1];
            if (nextRow < 0 || nextRow >= n || nextColumn < 0 || nextColumn >= n || matrix[nextRow][nextColumn] != 0)
                directionsIndex = (directionsIndex+1)%4;
            row = row + directions[directionsIndex][0];
            column = column + directions[directionsIndex][1];
        }
        return matrix;
    }
};
```
