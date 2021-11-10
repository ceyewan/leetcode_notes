### 46、全排列

```c
void huisu(int **ans, int *returnSize, int count, int *temp, int *nums, int numsSize, int *sign)
{
    // 如果确定好了所有的个数，就拷贝一份，加入ans中
    if (count == numsSize) {
        int *result = (int *)malloc(sizeof(int) * numsSize);
        // memcpy(result, temp, sizeof(int) * numsSize);用函数解决
        for (int i = 0; i < numsSize; i++)
            result[i] = temp[i];
        ans[(*returnSize)++] = result;
        return ;
    }
    else {
        for (int i = 0; i < numsSize; i++) {
            if (!sign[i]) {
                // 这个地方不能count++。改变count会改变回溯过程中这一层的状态，导致发生错误
                temp[count] = nums[i];
                sign[i] = 1;// 标记
                huisu(ans, returnSize, count + 1, temp, nums, numsSize, sign);
                sign[i] = 0;// 回溯
            }
        }
    }
}
int** permute(int* nums, int numsSize, int* returnSize, int** returnColumnSizes){
    // 6个数字，最多720种答案，所以我们申请1000个空间
    int **ans = (int **)malloc(sizeof(int *) * 1000);
    *returnSize = 0;
    // 临时存储一份答案
    int *temp = (int *)malloc(sizeof(int) * numsSize);
    // 标记下标为i的值是否访问
    int *sign = (int *)malloc(sizeof(int) * numsSize);
    // memset(sign, 0, sizeof(int) * numSize);
    for (int i = 0; i < numsSize; i++)
        sign[i] = 0;
    // 回溯算法，0表示现在确定好的数字的个数
    huisu(ans, returnSize, 0, temp, nums, numsSize, sign);
    *returnColumnSizes = (int *)malloc(sizeof(int) * (*returnSize));
    for (int i = 0; i < (*returnSize); i++)
        (*returnColumnSizes)[i] = numsSize;
    // free(temp);	free(sign);
    return ans;
}
```

### 784、字母大小写全排列

```c
void huisu(char *s, int count, int length, char *temp,int **ans, int *returnSize)
{
    if (count == length) {
        temp[count] = '\0';
        char *result = (char *)malloc(sizeof(char) * (length + 1));
        strcpy(result, temp);
        ans[(*returnSize)++] = result;
    } 
    // 总共就那么几种情况，手动回溯就好了
    else {
        if (s[count] <= '9' && s[count] >= '0') {
            temp[count] = s[count];
            huisu(s, count + 1, length, temp, ans, returnSize);
        }
        else if (s[count] >= 'a' && s[count] <= 'z') {
            temp[count] = s[count];
            huisu(s, count + 1, length, temp, ans, returnSize);
            temp[count] = s[count] - 32;
            huisu(s, count + 1, length, temp, ans, returnSize);
        }
        else {
            temp[count] = s[count];
            huisu(s, count + 1, length, temp, ans, returnSize);
            temp[count] = s[count] + 32;
            huisu(s, count + 1, length, temp, ans, returnSize);
        }
    }
}
char ** letterCasePermutation(char * s, int* returnSize){
    int length = strlen(s);
    char **ans = (char **)malloc(sizeof(char *) * pow(2, length));
    *returnSize = 0;
    char *temp = (char *)malloc(sizeof(char) * (length + 1));
    huisu(s, 0, length, temp, ans, returnSize);
    free(temp);
    return ans;
}
```

### 77、组合

```c
void huisu(int **ans, int *returnSize, int *temp, int *sign, int n, int k, int count)
{
    if (count == k) {
        int *result = (int *)malloc(sizeof(int) * k);
        memcpy(result, temp, sizeof(int) * k);
        ans[(*returnSize)++] = result;
    }
    else {
        // 后一个数字一定会比前一个数字要大，然后还得考虑第一个会越界
        for (int i = count > 0 ? temp[count - 1] + 1 : 1; i <= n; i++) {
            if (!sign[i]) {
                temp[count] = i;
                sign[i] = 1;// 标记
                huisu(ans, returnSize, temp, sign, n, k, count + 1);
                sign[i] = 0;// 回溯
            }
        }
    }
}
int** combine(int n, int k, int* returnSize, int** returnColumnSizes){
    // 总感觉这个1000000很丑，一定要想办法干掉它
    int **ans = (int **)malloc(sizeof(int *) * 1000000);
    *returnSize = 0;
    int sign[n + 1];// sign[j]表示数字j是否已经访问过了
    memset(sign, 0, sizeof(sign));
    int *temp = (int *)malloc(sizeof(int) * k);
    huisu(ans, returnSize, temp, sign, n, k, 0);
    *returnColumnSizes = (int *)malloc(sizeof(int) * (*returnSize));
    for (int i = 0; i < *returnSize; i++)
        (*returnColumnSizes)[i] = k;
    return ans;
}
```

完全不用sign来标记，因为后面的肯定比前面的大，所以不必标记。

```c
void huisu(int **ans, int *returnSize, int *temp, int n, int k, int count)
{
    if (count == k) {
        int *result = (int *)malloc(sizeof(int) * k);
        memcpy(result, temp, sizeof(int) * k);
        ans[(*returnSize)++] = result;
    }
    else {
        for (int i = count > 0 ? temp[count - 1] + 1 : 1; i <= n; i++) {
            // 剩余的长度一定不能比还需要的数字少
            if (n - i + 1 >= k - count) {
                temp[count] = i;
                huisu(ans, returnSize, temp, n, k, count + 1);
            }
        }
    }
}
int** combine(int n, int k, int* returnSize, int** returnColumnSizes){
    int **ans = (int **)malloc(sizeof(int *) * 100001);
    *returnSize = 0;
    int *temp = (int *)malloc(sizeof(int) * k);
    huisu(ans, returnSize, temp, n, k, 0);
    *returnColumnSizes = (int *)malloc(sizeof(int) * (*returnSize));
    for (int i = 0; i < *returnSize; i++)
        (*returnColumnSizes)[i] = k;
    return ans;
}
```

