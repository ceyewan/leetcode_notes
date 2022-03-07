### 45、跳跃游戏Ⅱ

>给你一个非负整数数组 nums ，你最初位于数组的第一个位置。
>
>数组中的每个元素代表你在该位置可以跳跃的最大长度。
>
>你的目标是使用最少的跳跃次数到达数组的最后一个位置。
>
>假设你总是可以到达数组的最后一个位置。

```c
int jump(int* nums, int numsSize){
    int dp[numsSize];
    memset(dp, 127, sizeof(dp));
    dp[0] = 0;
    for (int i = 1; i < numsSize; i++) {
        for (int j = 0; j < i; j++) {
            if (nums[j] + j >= i)
                dp[i] = fmin(dp[i], dp[j] + 1);
        }
    }
    return dp[numsSize - 1];
}
```

### 62、不同路径

>一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。
>
>机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。
>
>问总共有多少条不同的路径？

```c
int uniquePaths(int m, int n){
    int dp[m][n];
    memset(dp, 0, sizeof(int) * m * n);
    for (int i = 0; i < m; i++)
        dp[i][0] = 1;
    for (int j = 0; j < n; j++)
        dp[0][j] = 1;
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            dp[i][j] = dp[i][j - 1] + dp[i - 1][j];
        }
    }
    return dp[m - 1][n - 1];
}
```

