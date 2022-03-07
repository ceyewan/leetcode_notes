### 438、找到字符串中所有的异位词

将p的字母全部写入数组中，然后s来匹配。恰好匹配完说明找到了一个答案，不匹配的话就退出。然后恢复数组。时间复杂度是 `n*m` 。超过了5%的C语言提交。。。

```c
int* findAnagrams(char * s, char * p, int* returnSize){
    int len1 = strlen(s), len2 = strlen(p);
    int index = 0;
    int *ans = (int *)malloc(sizeof(int) * len1);
    if (len1 < len2) {
        *returnSize = 0;
        return ans;
    }
    int nums[26];
    memset(nums, 0, sizeof(nums));
    for (int i = 0; i < len2; i++) {
        nums[p[i] - 'a']++;
    }
    for (int i = 0; i < len1 - len2 + 1; i++) {
        if (nums[s[i] - 'a'] > 0) {
            int count = 0;
            for (int j = i; j < i + len2; j++) {
                if (nums[s[j] - 'a'] > 0) {
                    nums[s[j] - 'a']--;
                    count++;
                } else
                    break;
            }
            // 恰好匹配
            if (count == len2) {
                ans[index++] = i;
            }
            // 恢复数组
            for (int j = i; j < i + count; j++) {
                nums[s[j] - 'a']++;
            }
        }
    }
    *returnSize = index;
    return ans;
}
```

滑动窗口，快慢指针。如果标记数组中还有 s[fast] 那个字符，那么标记数组减一并且 fast 加一，直到标记数组没有 s[fast] 那个字符了。我们来判断 fast - slow 的长度是不是 len2 ，是的话说明这刚好和 p 匹配了。然后我们需要挪动 slow ，并且恢复标记数组，直到标记数组中有 s[fast] 字符（这样又可以去挪动 fast 了）。时间复杂度是 `n` ，超过95%的提交。

```c
int* findAnagrams(char * s, char * p, int* returnSize){
    int len1 = strlen(s), len2 = strlen(p);
    int index = 0;
    int *ans = (int *)malloc(sizeof(int) * len1);
    if (len1 < len2) {
        *returnSize = 0;
        return ans;
    }
    int nums[26];
    memset(nums, 0, sizeof(nums));
    for (int i = 0; i < len2; i++) {
        nums[p[i] - 'a']++;
    }
    int slow = 0, fast = 0;
    while (fast < len1) {
        // fast < len1 避免越界
        while (fast < len1 && nums[s[fast] - 'a'] > 0) {
            nums[s[fast] - 'a']--;
            fast++;
        }
        if (fast - slow == len2)
            ans[index++] = slow;
        while (fast < len1 && nums[s[fast] - 'a'] == 0) {
            nums[s[slow] - 'a']++;
            slow++;
        }
    }
    *returnSize = index;
    return ans;
}
```

明明之前做过类似的了，却还是会优先想到暴力算法，然后再想到好的做法。其实这不就是和算法入门 day6 的 567 题一样吗？不过好在至少熟练多了。

### 乘积小于K的子数组

暴力法呗，也就用了 1800ms 而已。

```c
int numSubarrayProductLessThanK(int* nums, int numsSize, int k){
    int count = 0;
    for (int i = 0; i < numsSize; i++) {
        int sum = 1;
        for (int j = i; j < numsSize; j++) {
            sum *= nums[j];
            if (sum < k)
                count++;
            else
                break;
        }
    }
    return count;
}
```

左右指针，slow 和 fast 之间是一个可行的答案，每次增加一个值进来会增加 fast - slow + 1 个答案。

```c
int numSubarrayProductLessThanK(int* nums, int numsSize, int k){
    if (k <= 1) return 0;
    int fast = 0, slow = 0, sum = 1, count = 0;
    for (fast = 0; fast < numsSize; fast++) {
        sum *= nums[fast];
        while (sum >= k) {
            sum /= nums[slow];
            slow++;
        }
        // nums[fast] >= k, 则 slow = fast + 1 , count += 0
        count += fast - slow + 1;
    }
    return count;
}
```

### 209、长度最小的子数组

还是滑动窗口，保留在 slow 和 fast 之间的区域作为答案。

```c
int minSubArrayLen(int target, int* nums, int numsSize){
    int fast = 0, slow = 0, min = INT_MAX, sum = 0;
    while (fast < numsSize) {
        sum += nums[fast];
        while (sum >= target) {
            min = min < (fast - slow + 1) ? min : (fast - slow + 1);
            sum -= nums[slow];
            slow++;
        }
        fast++;
    }
    if (min == INT_MAX)
        return 0;
    else 
        return min;
}
```

