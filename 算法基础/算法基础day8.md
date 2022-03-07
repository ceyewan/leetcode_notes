### 1091、二进制矩阵中的最短路径

>给你一个 n x n 的二进制矩阵 grid 中，返回矩阵中最短 **畅通路径** 的长度。如果不存在这样的路径，返回 -1 。
>
>二进制矩阵中的 **畅通路径** 是一条从 左上角 单元格（即，(0, 0)）到 右下角 单元格（即，(n - 1, n - 1)）的路径，该路径同时满足下述要求：
>
>路径途经的所有单元格都的值都是 0 。
>路径中所有相邻的单元格应当在 8 个方向之一 上连通（即，相邻两单元之间彼此不同且共享一条边或者一个角）。
>**畅通路径** 的长度 是该路径途经的单元格总数。

### 思路

`bfs` 搜索，找到最短路径。

```c
typedef struct {
    int i;
    int j;
    int count;
}qu;
qu queue[10001];
int dir[8][2] = {{1, 1}, {1, -1}, {-1, 1}, {-1, -1}, {1, 0}, {-1, 0}, {0, 1}, {0, -1}};
int bfs(int **grid, int m, int i, int j)
{
    if (grid[i][j] != 0)    return -1;
    int rear = 0, front = 0;
    grid[i][j] = 1;
    queue[front].i = i;
    queue[front].j = j;
    queue[front++].count = 1;
    while (rear < front) {
        int x = queue[rear].i;
        int y = queue[rear].j;
        int count = queue[rear++].count;
        for (int k = 0; k < 8; k++) {
            int x1 = x + dir[k][0];
            int y1 = y + dir[k][1];
            if (0 <= x1 && x1 < m && 0 <= y1 && y1 < m && grid[x1][y1] == 0) {
                grid[x1][y1] = 1;
                if (x1 == m- 1 && y1 == m - 1)
                    return count + 1;
                else {
                    queue[front].i = x1;
                    queue[front].j = y1;
                    queue[front++].count = count + 1;
                }
            }
        }
    }
    return -1;
}
int shortestPathBinaryMatrix(int** grid, int gridSize, int* gridColSize){
    int m = gridSize;
    if (m == 1 && grid[0][0] == 0)  return 1;
    int count = bfs(grid, m, 0, 0);
    return count;
}
```

用动态规划的做法，失败了。一个数，必然是从它的八个方向来的，所以选择八个方向的最小值再加一就是它的最小值。但是也正是因为有八个方向，依赖性过于复杂，所以不行。`dp[i][j]`可能依赖 `dp[i + 1][j + 1]`，那么我们先算第 i 行就求不出来 `dp[i][j]` ，因为它依赖的 `dp[i + 1][j + 1]` 还没算。波浪形的依赖关系就没法算出来，用回溯又过于复杂。下面的做法是失败的。

```c
int dir[8][2] = {{1, 1}, {1, -1}, {-1, 1}, {-1, -1}, {1, 0}, {-1, 0}, {0, 1}, {0, -1}};
int shortestPathBinaryMatrix(int** grid, int gridSize, int* gridColSize){
    int m = gridSize;
    int dp[m][m];
    memset(dp, 127, sizeof(dp));
    if (grid[0][0] != 0 || grid[m - 1][m - 1] != 0) return -1;
    dp[0][0] = 1;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < m; j++) {
            if (grid[i][j] == 0) {
                for (int k = 0; k < 8; k++) {
                    int x1 = i + dir[k][0];
                    int y1 = j + dir[k][1];
                    if (0 <= x1 && x1 < m && 0 <= y1 && y1 < m && grid[x1][y1] == 0) {
                        dp[i][j] = fmin(dp[i][j], dp[x1][y1] + 1);
                    }
                }
            }
        }
    }
    return dp[m - 1][m - 1];
}
```

### 130、被围绕的区域

>给你一个 `m x n` 的矩阵 `board` ，由若干字符 `'X'` 和 `'O'` ，找到所有被 `'X'` 围绕的区域，并将这些区域里所有的 `'O'` 用 `'X'` 填充。

### 思路

意思就是说靠近边界的 `O` 不会被填充，其他的都会。那么我们就搜索靠近边界的 `O` 即可。

```c
int sign[201][201];
int dir[4][2] = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
// 将相邻的O全部标记
void dfs(char **board, int m, int n, int i, int j)
{
    sign[i][j] = 1;
    for (int k = 0; k < 4; k++) {
        int x = i + dir[k][0];
        int y = j + dir[k][1];
        if (0 <= x && x < m && 0 <= y && y < n && board[x][y] == 'O' && !sign[x][y])
            dfs(board, m, n, x, y);
    }
}
void solve(char** board, int boardSize, int* boardColSize){
    int m = boardSize, n = boardColSize[0];
    // 标记是否为边界O
    memset(sign, 0, sizeof(sign));
    // 找到边界O
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (i == 0 || i == m - 1 || j == 0 || j == n - 1) {
                if (board[i][j] == 'O')
                    dfs(board, m, n, i, j);
            }
        }
    }
    // 非标记的全部设为O
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (!sign[i][j])
                board[i][j] = 'X';
        }
    }
}
```

### 797、所有可能的路径

>给你一个有 n 个节点的 有向无环图（DAG），请你找出所有从节点 0 到节点 n-1 的路径并输出（不要求按特定顺序）
>
>二维数组的第 i 个数组中的单元都表示有向图中 i 号节点所能到达的下一些节点，空就是没有下一个结点了。
>
>译者注：有向图是有方向的，即规定了 a→b 你就不能从 b→a 。
>
>输入：graph = [[1,2],[3],[3],[]]
>输出：[[0,1,3],[0,2,3]]
>解释：有两条路径 0 -> 1 -> 3 和 0 -> 2 -> 3

### 思路

dfs +  回溯。

```c
int basicSize = 8000;
// end表示temp中最后一个数，count表示temp的长度
void dfs(int **graph, int graphSize, int *graphColSize, int **ans, int *temp, int *returnSize, int **returnColumnSizes, int end, int count)
{
    // temp的最后一个数已经是结果了，就增加一个答案
    if (end == graphSize - 1) {
        int *result = (int *)malloc(sizeof(int) * count);
        for (int i = 0; i < count; i++) {
            result[i] = temp[i];
        }
        // 动态分配内存会报错，不知道为什么
        // if (*returnSize == basicSize) {
        //     basicSize *= 2;
        //     ans = realloc(ans, sizeof(int *) * basicSize);
        //     *returnColumnSizes = realloc(*returnColumnSizes, sizeof(int) * basicSize);
        // }
        ans[*returnSize] = result;
        (*returnColumnSizes)[(*returnSize)++] = count;
    }
    // 遍历
    for (int i = 0; i < graphColSize[end]; i++) {
        temp[count] = graph[end][i];// 写入临时结果
        dfs(graph, graphSize, graphColSize,ans, temp, returnSize, returnColumnSizes, graph[end][i], count + 1);
    }
}
int** allPathsSourceTarget(int** graph, int graphSize, int* graphColSize, int* returnSize, int** returnColumnSizes){
    int **ans = (int **)malloc(sizeof(int *) * basicSize);
    *returnColumnSizes = (int *)malloc(sizeof(int) * basicSize);
    *returnSize = 0;
    // temp存储一组临时结果
    int *temp = (int *)malloc(sizeof(int) * graphSize);
    temp[0] = 0;
    dfs(graph, graphSize, graphColSize, ans, temp, returnSize, returnColumnSizes, 0, 1);
    return ans;
}
```

