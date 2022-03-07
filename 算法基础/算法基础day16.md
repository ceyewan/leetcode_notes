### 300、最长递增子序列

[最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

>Given an integer array `nums`, return the length of the longest strictly increasing subsequence.
>
>A subsequence is a sequence that can be derived from an array by deleting some or no elements without changing the order of the remaining elements. For example, [3,6,2,7] is a subsequence of the array [0,3,1,6,2,2,7].
>
>Example 1:
>
>```
>Input: nums = [10,9,2,5,3,7,101,18]
>Output: 4
>Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4.
>
>Input: nums = [0,1,0,3,2,3]
>Output: 4
>
>Input: nums = [7,7,7,7,7,7,7]
>Output: 1
>```

```c
int lengthOfLIS(int* nums, int numsSize){
    int dp[numsSize];// dp[i]表示以i结尾的递增自序列的长度
    dp[0] = 1;
    int max = 1;
    for (int i = 0; i < numsSize; i++) {
        dp[i] = 1;
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                dp[i] = fmax(dp[i], dp[j] + 1);
                max = max > dp[i] ? max : dp[i];
            }
        }
    }
    return max;
}
```

### 673、最长递增子序列的个数

>Given an integer array `nums`, return the number of longest increasing subsequences.
>
>Notice that the sequence has to be strictly increasing.
>
>
>
>Example :
>
>```
>Input: nums = [1,3,5,4,7]
>Output: 2
>Explanation: The two longest increasing subsequences are [1, 3, 4, 7] and [1, 3, 5, 7].
>
>Input: nums = [2,2,2,2,2]
>Output: 5
>Explanation: The length of longest continuous increasing subsequence is 1, and there are 5 subsequences' length is 1, so output 5.
>```

```c
int findNumberOfLIS(int* nums, int numsSize){
    int dp[numsSize];// dp[i]表示以i结尾的递增子序列的最大长度
    int count[numsSize];// count[i]表示以i结尾的递增子序列的组合数量
    int ans = 0, max = 0;
    for (int i = 0; i < numsSize; i++) {
        dp[i] = 1; count[i] = 1;
        for (int j = 0; j < i; j++) {
            if (nums[i] > nums[j]) {
                if (dp[j] + 1 > dp[i]) {
                    dp[i] = dp[j] + 1;
                    count[i] = count[j];
                } else if (dp[j] + 1 == dp[i]) {
                    count[i] += count[j];
                }
            }
        }
        if (dp[i] > max) {
            max = dp[i];
            ans = count[i];
        } else if (dp[i] == max){
            ans += count[i];
        }
    }
    return ans;
}
```



