### 695、岛屿的最大面积

`bfs`

```c
typedef struct {
    int i;
    int j;
}qu;
int dir[4][2] = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
qu queue[2500];
int bfs(int **grid, int m, int n, int i, int j)
{
    int rear = 0, front = 0;
    queue[front].i = i;
    queue[front].j = j;
    front++;
    grid[i][j] = 0;
    int count = 1;
    while (front > rear) {
        int row = queue[rear].i;
        int col = queue[rear].j;
        rear++;
        for (int i = 0; i < 4; i++) {
            int row1 = row + dir[i][0];
            int col1 = col + dir[i][1];
            if (0 <= row1 && row1 < m && 0 <= col1 && col1 < n && grid[row1][col1] == 1) {
                queue[front].i = row1;
                queue[front].j = col1;
                front++;
                grid[row1][col1] = 0;
                count++;
            }
        }
    }
    return count;
}
int maxAreaOfIsland(int** grid, int gridSize, int* gridColSize){
    int m = gridSize, n = gridColSize[0];
    int max = 0, count = 0;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == 1)
                count = bfs(grid, m, n, i, j);
            max = max > count ? max : count;
        }
    }
    return max;
}


```

`dfs` + 递归

```c
int dir[4][2] = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
int dfs(int **grid, int m, int n, int i, int j)
{
    grid[i][j] = 0;
    int count = 1;
    for (int k = 0; k < 4; k++) {
        int i1 = i + dir[k][0];
        int j1 = j + dir[k][1];
        if (0 <= i1 && i1 < m && 0 <= j1 && j1 < n && grid[i1][j1] == 1) {
            count += dfs(grid, m, n, i1, j1);
        }
    }
    return count;
}
int maxAreaOfIsland(int** grid, int gridSize, int* gridColSize){
    int m = gridSize, n = gridColSize[0];
    int max = 0, count = 0;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == 1)
                count = dfs(grid, m, n, i, j);
            max = max > count ? max : count;
        }
    }
    return max;
}
```

`dfs` + 栈

```c
typedef struct {
    int i;
    int j;
}Stack;
int dir[4][2] = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
Stack stack[2500];
int dfs(int **grid, int m, int n, int i, int j)
{
    stack[0].i = i;
    stack[0].j = j;
    int top = 0;
    grid[i][j] = 0;
    int count = 1;
    while (top >= 0) {
        int x = stack[top].i;
        int y = stack[top].j;
        top--;
        for (int k = 0; k < 4; k++) {
            int x1 = x + dir[k][0];
            int y1 = y + dir[k][1];
            if (0 <= x1 && x1 < m && 0 <= y1 && y1 < n && grid[x1][y1] == 1) {
                grid[x1][y1] = 0;
                top++;
                stack[top].i = x1;
                stack[top].j = y1;
                count++;
            }
        }
    }
    return count;
}
int maxAreaOfIsland(int** grid, int gridSize, int* gridColSize){
    int m = gridSize, n = gridColSize[0];
    int max = 0, count = 0;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == 1)
                count = dfs(grid, m, n, i, j);
            max = max > count ? max : count;
        }
    }
    return max;
}
```

### 733、图像渲染

```c
typedef struct {
    int i;
    int j;
}qu;
int dir[4][2] = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
qu queue[2500];
void bfs(int **image, int m, int n, int i, int j, int newColor)
{
    int rear = 0, front = 0;
    queue[front].i = i;
    queue[front].j = j;
    front++;
    int sign = image[i][j];
    image[i][j] = newColor;
    while (front > rear) {
        int x = queue[rear].i;
        int y = queue[rear].j;
        rear++;
        for (int i = 0; i < 4; i++) {
            int x1 = x + dir[i][0];
            int y1 = y + dir[i][1];
            if (0 <= x1 && x1 < m && 0 <= y1 && y1 < n && image[x1][y1] == sign) {
                queue[front].i = x1;
                queue[front].j = y1;
                front++;
                image[x1][y1] = newColor;
            }
        }
    }
}
int** floodFill(int** image, int imageSize, int* imageColSize, int sr, int sc, int newColor, int* returnSize, int** returnColumnSizes){
    int m = imageSize, n = imageColSize[0];
    *returnSize = m;
    // 这个必须要malloc，所有才传入**，否则传入一个数组不就行了吗
    *returnColumnSizes = (int *)malloc(sizeof(int) * imageSize);
    for (int i = 0; i < m; i++) {
        (*returnColumnSizes)[i] = n;
    }
    if (image[sr][sc] == newColor)
        return image;
    bfs(image, m, n, sr, sc, newColor);
    return image;
}
```

