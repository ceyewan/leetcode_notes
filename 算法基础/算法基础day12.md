### 213、打家劫舍Ⅱ

>你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 围成一圈 ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。
>
>给定一个代表每个房屋存放金额的非负整数数组，计算你 在不触动警报装置的情况下 ，今晚能够偷窃到的最高金额。

分两种选择，从第一间到倒数第二间或者第二间到最后一间。

```c
int maxrob(int *nums, int start, int end)
{
    int dp[end - start];
    memset(dp, 0, sizeof(dp));
    dp[0] = nums[start];
    dp[1] = fmax(nums[start], nums[start + 1]);
    for (int i = 2; i < end - start; i++) {
        dp[i] = fmax(dp[i - 1], dp[i - 2] + nums[start + i]);
    }
    return dp[end - start - 1];
}
int rob(int* nums, int numsSize){
    if (numsSize == 1) {
        return nums[0];
    } else if (numsSize == 2) {
        return nums[0] > nums[1] ? nums[0] : nums[1];
    } else {
        return fmax(maxrob(nums, 0, numsSize - 1), maxrob(nums, 1, numsSize));
    }
}
```

### 55、跳跃游戏

>给定一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。
>
>数组中的每个元素代表你在该位置可以跳跃的最大长度。
>
>判断你是否能够到达最后一个下标。

```c
bool canJump(int* nums, int numsSize){
    int dp[numsSize]; // dp[i]表示在下标为i处能够跳跃的最大值
    memset(dp, 0, sizeof(dp));
    dp[0] = nums[0];
    
    for (int i = 1; i < numsSize; i++) {
        if (dp[i - 1] == 0) return false;
        dp[i] = fmax(dp[i - 1] - 1, nums[i]);
    }
    return true;
}
```

