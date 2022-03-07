### 78、子集

>给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。
>
>解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。数组长度不超过 10 。

### 思路

回溯，遇到一个可能组合就写入 ans ，第一个数从所有数中选，第二个数从第一个之后的选，以此类推。

```c
// count代表这一组子集的长度
// sign表示前一个数的下标是什么，然后从后面选择，这样可以避免重复，不会出现{1，3}和{3， 1}这种
void huisu(int *nums, int numsSize, int **ans, int *returnSize, int **returnColumnSizes, int *temp, int count, int sign)
{   
    // 第一组答案是空集，count = 0
    int *result = (int *)malloc(sizeof(int) * count);
    memcpy(result, temp, sizeof(int) * count);
    ans[(*returnSize)] = result;
    (*returnColumnSizes)[(*returnSize)++] = count;
    for (int i = sign + 1; i < numsSize; i++) {
        temp[count] = nums[i];
        huisu(nums, numsSize, ans, returnSize, returnColumnSizes, temp, count + 1, i);
    }
}
int** subsets(int* nums, int numsSize, int* returnSize, int** returnColumnSizes){
    int **ans = (int **)malloc(sizeof(int *) * 10000);
    *returnSize = 0;
    *returnColumnSizes = (int *)malloc(sizeof(int) * 10000);
    int *temp = (int *)malloc(sizeof(int) * 10);
    huisu(nums, numsSize, ans, returnSize, returnColumnSizes, temp, 0, -1);
    return ans;
}
```

### 90、子集Ⅱ

>给你一个整数数组 nums ，其中可能包含重复元素，请你返回该数组所有可能的子集（幂集）。
>
>解集 不能 包含重复的子集。返回的解集中，子集可以按 任意顺序 排列。
>

### 思路

要达到去重的目的，第一步需要排序，第二步需要跳过重复的元素。我们可以用一个二维数组来记录第count层数字index是否用过了，跳出这一层的时候要恢复。

```c
int comp(void const *a, void const *b)
{
    return *(int *)a - *(int *)b;
}
void huisu(int *nums, int numsSize, int **ans, int *returnSize, int **returnColumnSizes, int *temp, int count, int sign)
{   
    int *result = (int *)malloc(sizeof(int) * count);
    memcpy(result, temp, sizeof(int) * count);
    ans[(*returnSize)] = result;
    (*returnColumnSizes)[(*returnSize)++] = count;
    for (int i = sign + 1; i < numsSize; i++) {
        // 第一个元素或者和前一个元素不同
        if (i == sign + 1 || nums[i] != nums[i - 1]) {
            temp[count] = nums[i];
            huisu(nums, numsSize, ans, returnSize, returnColumnSizes, temp, count + 1, i);
        }
    }
}
int** subsetsWithDup(int* nums, int numsSize, int* returnSize, int** returnColumnSizes){
    qsort(nums, numsSize, sizeof(int), comp);
    int **ans = (int **)malloc(sizeof(int *) * 1024);
    *returnSize = 0;
    *returnColumnSizes = (int *)malloc(sizeof(int) * 1024);
    int *temp = (int *)malloc(sizeof(int) * 10);
    huisu(nums, numsSize, ans, returnSize, returnColumnSizes, temp, 0, -1);
    return ans;
}
```

