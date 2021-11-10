### 70、爬楼梯

```c
int climbStairs(int n){
    int dp[n + 1];
    if (n == 1) return 1;
    if (n == 2) return 2;
    memset(dp, 127, sizeof(dp));
    dp[1] = 1;dp[2] = 2;
    for (int i = 3; i < n + 1; i++)
        dp[i] = dp[i - 1] + dp[i - 2];
    return dp[n];
}
```

### 198、打家劫舍

```c
int rob(int* nums, int numsSize){
    if (numsSize == 1)
        return nums[0];
    int dp[numsSize]; //偷窃到第i户的最高金额
    dp[0] = nums[0];
    dp[1] = fmax(nums[0], nums[1]);
    for (int i = 2; i < numsSize; i++)
        dp[i] = fmax(dp[i - 1], dp[i - 2] + nums[i]);
    return dp[numsSize - 1];
}
```

### 120、三角形最小路径和

```c
int minimumTotal(int** triangle, int triangleSize, int* triangleColSize){
    int m = triangleSize; int n = triangleColSize[triangleSize - 1];
    int dp[m][n];// d[i][j]表示到达第i行第j列最小的路径和（从0开始计数）
    memset(dp, 127, sizeof(dp));
    dp[0][0] = triangle[0][0];
    for (int i = 1; i < triangleSize; i++) 
        dp[i][0] = dp[i - 1][0] + triangle[i][0];
    for (int i = 1; i < triangleSize; i++) {
        for (int j = 1; j < triangleColSize[i]; j++) {
            dp[i][j] = fmin(dp[i - 1][j], dp[i - 1][j - 1]) + triangle[i][j];
        }
    }
    int min = INT_MAX;
    for (int i = 0; i < n; i++)
        min = min < dp[m - 1][i] ? min : dp[m - 1][i];
    return min;
}
```



