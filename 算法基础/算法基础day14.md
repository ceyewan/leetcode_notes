### 5、最长回文子串

>给你一个字符串 `s`，找到 `s` 中最长的回文子串。

```c
char * longestPalindrome(char * s){
    int len = strlen(s);
    if (len < 2)    return s;
    int max_length = 1, start = 0;
    bool dp[len][len]; //dp[i][j]表示从第i个到第j个是否是回文数
    for (int i = 0; i < len; i++) {
        dp[i][i] = true;
    }
    for (int i = 0; i < len - 1; i++) {
        if (s[i] == s[i + 1]) {
            max_length = 2;
            start = i;
            dp[i][i + 1] = true;
        }
        else 
            dp[i][i + 1] = false;
    }
    for (int L = 3; L <= len; L++) {
        for (int i = 0; i < len; i++) {
            int j = i + L - 1;
            if (j >= len)
                break;
            if (s[i] == s[j]) {
                dp[i][j] = dp[i + 1][j - 1];
                if (dp[i][j]) {
                    max_length = L;
                    start = i;
                }
            }
            else
                dp[i][j] = false;
        }
    }
    char *ans = (char *)malloc(sizeof(char) * (max_length + 1));
    for (int i = 0; i < max_length; i++)
        ans[i] = s[start + i];
    ans[max_length] = '\0';
    return ans;
}
```

### 413、等差数列划分

>如果一个数列 至少有三个元素 ，并且任意两个相邻元素之差相同，则称该数列为等差数列。
>
>例如，[1,3,5,7,9]、[7,7,7,7] 和 [3,-1,-5,-9] 都是等差数列。
>给你一个整数数组 nums ，返回数组 nums 中所有为等差数组的 子数组 个数。
>
>子数组 是数组中的一个连续序列。

```c
int numberOfArithmeticSlices(int* nums, int numsSize){
    if (numsSize < 3)   return 0;
    bool dp[numsSize][numsSize];// dp[i][j]表示下标i到下标j是否是等差数列
    memset(dp, true, sizeof(dp));
    for (int L = 3; L <= numsSize; L++) {
        for (int i = 0; i < numsSize; i++) {
            int j = i + L - 1;
            if (j >= numsSize)  break;
            if (nums[i + 1] - nums[i] == nums[j] - nums[j - 1] && 
            (L == 3 || nums[j] - nums[j - 1] == (nums[j - 1] - nums[i + 1]) / (L - 3)))
                dp[i][j] = dp[i + 1][j - 1];
            else 
                dp[i][j] = false;
        }
    }
    int count = 0;
    for (int i = 0; i < numsSize; i++) {
        for (int j = i + 2; j < numsSize; j++) {
            if (dp[i][j])
                count++;
        }
    }
    return count;
}
```

