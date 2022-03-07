

### 47、全排列Ⅱ

>给定一个可包含重复数字的序列 `nums` ，**按任意顺序** 返回所有不重复的全排列。
>
>**提示：**
>
>- `1 <= nums.length <= 8`
>- `-10 <= nums[i] <= 10`

```
示例：
输入：nums = [1,1,2]
输出：
[[1,1,2],
 [1,2,1],
 [2,1,1]]
```



### 思路

和全排列一样，用回溯的方法解决，不一样的地方就是这里需要去除重复的部分。`sign[i]` 表示第 `i` 个数在前面已经用过了，所以这里不能用。`judge[count][i]` 表示第 `count` 层数字 `i` 已经用过了，在这一层就不能再用了。并且退出该层的时候需要恢复。（ 1 开头的时候第二层数字 3 用过了，到 2 开始的时候第二层还是可以用数字 3 的。不能让 3 在这一层永远不出现了）。

```c
int judge[8][21];// 根据提示确定的
// sign[i] 表示第 `i` 个数在前面已经用过了，所以这里不能用
// judge[count][i] 表示第 `count` 层数字 `i` 已经用过了，在这一层就不能再用了
void huisu(int **ans, int *returnSize, int count, int *temp, int *nums, int numsSize, int *sign)
{
    if (count == numsSize) {
        int *result = (int *)malloc(sizeof(int) * numsSize);
        memcpy(result, temp, sizeof(int) * numsSize);
        ans[(*returnSize)++] = result;
        return ;
    }
    else {
        for (int i = 0; i < numsSize; i++) {
            if (!sign[i] && judge[count][nums[i] + 10] == 0) {
                judge[count][nums[i] + 10] = 1;
                temp[count] = nums[i];
                sign[i] = 1;
                huisu(ans, returnSize, count + 1, temp, nums, numsSize, sign);
                sign[i] = 0;
            }
        }
        // 退出该层时恢复
        memset(judge[count], 0, sizeof(judge[count]));
    }
}
int** permuteUnique(int* nums, int numsSize, int* returnSize, int** returnColumnSizes){
    memset(judge, 0, sizeof(judge));
    int **ans = (int **)malloc(sizeof(int *) * 1000);
    *returnSize = 0;
    int *temp = (int *)malloc(sizeof(int) * numsSize);
    int *sign = (int *)malloc(sizeof(int) * numsSize);
    memset(sign, 0, sizeof(int) * numsSize);
    huisu(ans, returnSize, 0, temp, nums, numsSize, sign);
    *returnColumnSizes = (int *)malloc(sizeof(int) * (*returnSize));
    for (int i = 0; i < (*returnSize); i++)
        (*returnColumnSizes)[i] = numsSize;
    free(temp);
    free(sign);
    return ans;
}
```

### 39、组合总数

>给定一个无重复元素的正整数数组 candidates 和一个正整数 target ，找出 candidates 中所有可以使数字和为目标数 target 的唯一组合。
>
>candidates 中的数字可以无限制重复被选取。如果至少一个所选数字数量不同，则两种组合是唯一的。 
>
>对于给定的输入，保证和为 target 的唯一组合数少于 150 个。

```
示例：
输入: candidates = [2,3,6,7], target = 7
输出: [[7],[2,2,3]]
```

### 代码

```c
#define basicSize 150
// sign表示前一个数的下标
void huisu(int *candidates, int candidatesSize, int target, int **ans, int *returnSize, int **returnColumnSizes, int count, int sum,int sign, int *temp)
{
    if (sum > target)   return;
    if (sum == target) {
        int *result = (int *)malloc(sizeof(int) * count);
        for (int i = 0; i < count; i++) {
            result[i] = temp[i];
        }
        ans[*returnSize] = result;
        (*returnColumnSizes)[(*returnSize)++] = count;
        return;
    }
    for (int i = sign; i < candidatesSize; i++) {
        temp[count] = candidates[i];
        huisu(candidates, candidatesSize, target, ans, returnSize, returnColumnSizes, count + 1, sum + candidates[i], i, temp);
    }
}
int comp(void const *a, void const *b)
{
    return *(int *)a - *(int *)b;
}
int** combinationSum(int* candidates, int candidatesSize, int target, int* returnSize, int** returnColumnSizes){
    qsort(candidates, candidatesSize, sizeof(int), comp);
    *returnSize = 0;
    int **ans = (int **)malloc(sizeof(int *) * basicSize);
    *returnColumnSizes = (int *)malloc(sizeof(int) * basicSize);
    int *temp = (int *)malloc(sizeof(int) * target);
    huisu(candidates, candidatesSize, target, ans, returnSize, returnColumnSizes, 0, 0, 0, temp);
    return ans;
}
```

### 40、组合总数Ⅱ

>给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
>
>candidates 中的每个数字在每个组合中只能使用一次。
>
>注意：解集不能包含重复的组合。 

```
示例：
输入: candidates = [10,1,2,7,6,1,5], target = 8,
输出:
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

### 思路

还是一样的，去重。这里解释一下什么时候可以要数组什么时候直接不等于前一个就可以了。和第一题的区别在于，第一题的循环是从头开始的，我也不知道哪个用过了没用过，所以再加个数组来判断好得多。这一题是从 sign + 1 开始的，前面的前面都用过了，所以如果是这层的第一个，可以。如果是其他的，就要和前面的那个不同（因为前面的那个也是在这一层用过了）

```c
void huisu(int *candidates, int candidatesSize, int target, int **ans, int *returnSize, int **returnColumnSizes, int count, int sum,int sign, int *temp)
{
    if (sum > target)   return;
    if (sum == target) {
        int *result = (int *)malloc(sizeof(int) * count);
        for (int i = 0; i < count; i++) {
            result[i] = temp[i];
        }
        ans[*returnSize] = result;
        (*returnColumnSizes)[(*returnSize)++] = count;
        return;
    }
    for (int i = sign + 1; i < candidatesSize; i++) {
        // 去重
        if (i == sign + 1 || candidates[i] != candidates[i - 1]) {
            temp[count] = candidates[i];
            huisu(candidates, candidatesSize, target, ans, returnSize, returnColumnSizes, count + 1, sum + candidates[i], i, temp);
        }
    }
}
int comp(void const *a, void const *b)
{
    return *(int *)a - *(int *)b;
}
int** combinationSum2(int* candidates, int candidatesSize, int target, int* returnSize, int** returnColumnSizes){
    qsort(candidates, candidatesSize, sizeof(int), comp);
    *returnSize = 0;
    int **ans = (int **)malloc(sizeof(int *) * 150);
    *returnColumnSizes = (int *)malloc(sizeof(int) * 150);
    int *temp = (int *)malloc(sizeof(int) * target);
    huisu(candidates, candidatesSize, target, ans, returnSize, returnColumnSizes, 0, 0, -1, temp);
    return ans;
}
```

