### 200、岛屿数量

注意，最好不要修改原数组，我们可以增加一个标记数组来判断。

```c
typedef struct {
    int i;
    int j;
}queue;
queue qu[90000];
int dir[4][2] = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
void bfs(char **grid, int m, int n, int i, int j)
{
    int rear = 0, front = 0;
    grid[i][j] = '0';
    qu[front].i = i;
    qu[front++].j = j;
    while (front > rear) {
        int x = qu[rear].i;
        int y = qu[rear++].j;
        for (int k = 0; k < 4; k++) {
            int x1 = x + dir[k][0];
            int y1 = y + dir[k][1];
            if (0 <= x1 && x1 < m && 0 <= y1 && y1 < n && grid[x1][y1] == '1') {
                grid[x1][y1] = 0;
                qu[front].i = x1;
                qu[front++].j = y1;
            }
        }
    }
}
int numIslands(char** grid, int gridSize, int* gridColSize){
    int m = gridSize, n = gridColSize[0];
    int count = 0;
    for(int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == '1') {
                count++;
                bfs(grid, m, n, i, j);
            }
        }
    }
    return count;
}
```

### 省份数量

>有 n 个城市，其中一些彼此相连，另一些没有相连。如果城市 a 与城市 b 直接相连，且城市 b 与城市 c 直接相连，那么城市 a 与城市 c 间接相连。
>
>省份 是一组直接或间接相连的城市，组内不含其他没有相连的城市。
>
>给你一个 n x n 的矩阵 `isConnected` ，其中 `isConnected[i][j] = 1 `表示第 i 个城市和第 j 个城市直接相连，而 `isConnected[i][j] = 0` 表示二者不直接相连。
>
>返回矩阵中 省份 的数量。
>

这道题其实杨博洋给我们做过，可惜我是废物，还是不记得怎么写。于是乎，只能用自己时间复杂度比较高的方法了。

```c
// 这里是需要递归处理的，遇到一个非0的就得去找它的连接
void mod(int **isConnected, int m, int n, int i, int j)
{
    for (int k = 0; k < n; k++) {
        if (isConnected[i][k] == 1) {
            isConnected[i][k] = 0;
            mod(isConnected, m, n, i, k);
        }
        if (isConnected[j][k] == 1) {
            isConnected[j][k] = 0;
            mod(isConnected, m, n, j, k);
        }
    }
    for (int k = 0; k < m; k++) {
        if (isConnected[k][i] == 1) {
            isConnected[k][i] = 0;
            mod(isConnected, m, n, k, i);
        }
        if (isConnected[k][j] == 1) {
            isConnected[k][j] = 0;
            mod(isConnected, m, n, k, j);
        }
    }
}
int findCircleNum(int** isConnected, int isConnectedSize, int* isConnectedColSize){
    int m = isConnectedSize, n = isConnectedColSize[0];
    int count = 0;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (isConnected[i][j] == 1) {
                count++;
                mod(isConnected, m, n, i, j);
            }
        }
    }
    return count;
}
```

看了题解之后恍然大明白，尝试一下自己的语言表达能力能不能把思路讲清楚。矩阵是` n * n `的，说明有 n 个城市，那么我们只要考虑一行，或者一列就能把所有的城市都考虑到。因此我们可以先考虑一列，那么第一个是第 0 行，也就是第 0 个城市，然后再看这一行，如果出现了 1 ， 比如` isConnected[0][2] `是 1 ，说明城市 0 和 2 是相连的。那我们就标记城市 2 是访问过的了，然后进入递归函数考虑城市 2 ，完了之后，积蓄扫描这一行，直到城市 0 相连的全部考虑完，我们回到列的考虑上来。再考虑第二个是第 1 行，再考虑第四个是第 3 行（第三个是第 2 行，即城市 2 已经考虑过了。

dfs

```c
void dfs(int** isConnected, int* visited, int m, int i) {
    for (int j = 0; j < m; j++) {
        if (isConnected[i][j] == 1 && !visited[j]) {
            visited[j] = 1;
            dfs(isConnected, visited, m, j);
        }
    }
}

int findCircleNum(int** isConnected, int isConnectedSize, int* isConnectedColSize) {
    int m = isConnectedSize;
    int visited[m];// 标记城市 i 有没有访问过
    memset(visited, 0, sizeof(visited));
    int count = 0;
    for (int i = 0; i < m; i++) {
        if (!visited[i]) {
            dfs(isConnected, visited, m, i);
            count++;
        }
    }
    return count;
}
```

bfs

```c
int queue[200];
void bfs(int **isConnected, int m, int *visit, int i)
{
    int rear = 0, front = 0;
    queue[front++] = i;
    while (front > rear) {
        int city = queue[rear++];
        for (int j = 0; j < m; j++) {
            if (isConnected[city][j] == 1 && !visit[j]) {
                visit[j] = 1;
                queue[front++] = j;
            }
        }
    }
}

int findCircleNum(int** isConnected, int isConnectedSize, int* isConnectedColSize){
    int m = isConnectedSize;
    int count = 0;
    int visit[m];
    memset(visit, 0, sizeof(visit));
    for (int i = 0; i < m; i++) {
        if (!visit[i]) {
            count++;
            visit[i] = 1;
            bfs(isConnected, m, visit, i);
        }
    }
    return count;
}
```

