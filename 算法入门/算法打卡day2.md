### 977、有序数组的平方

先全部平方，然后再排序。时间复杂度`nlogn`，这样就没用到有序这个条件了。

```c
int comp(const void *a, const void *b)
{
    return *(int *)a - *(int *)b;
}
int* sortedSquares(int* nums, int numsSize, int* returnSize){
    int *result = (int *)malloc(sizeof(int) * numsSize);
    for (int i = 0; i < numsSize; i++) {
        result[i] = nums[i] * nums[i];
    }
    qsort(result, numsSize, sizeof(int), comp);
    *returnSize = numsSize;
    return result;
}
```

双指针，全正就正序，全负就逆序。有正有负就讨论中间点，双指针，一个向左一个向右，选两个中较小的那个。

```c
int* sortedSquares(int* nums, int numsSize, int* returnSize){
    *returnSize = numsSize;
    int *result = (int *)malloc(sizeof(int) * numsSize);
    if (nums[0] >= 0) {
        for (int i = 0; i < numsSize; i++) {
            result[i] = nums[i] * nums[i];
        }
        return result;
    }
    else if (nums[numsSize - 1] <= 0) {
        for (int i = 0; i < numsSize; i++) {
            result[i] = nums[numsSize - 1 - i] * nums[numsSize - 1 - i];
        }
        return result;
    }
    int left = 0, right = 0;
    for (int i = 0; i < numsSize - 1; i++) {
        if (nums[i] < 0 && nums[i + 1] >= 0) {
            left = i;
            right = i + 1;
            break;
        }
    }
    int i = 0;
    while (i < numsSize) {
        if ((left >= 0 && right < numsSize && nums[left] + nums[right] < 0) || left == -1) {
            result[i] = nums[right] * nums[right];
            right++;
        }
        else {
            result[i] = nums[left] * nums[left];
            left--;
        }
        i++;
    }
    return result;
}
```

好长啊，好麻烦，就不能从两边到中间选较大的那个，放后面吗？

```c
int* sortedSquares(int* nums, int numsSize, int* returnSize){
    int *result = (int *)malloc(sizeof(int) * numsSize);
    *returnSize = numsSize;
    int left = 0, right = numsSize - 1;
    int i = numsSize - 1;
    while (i >= 0) {
        if (nums[left] + nums[right] <= 0) {
            result[i] = nums[left] * nums[left];
            left++;
        }
        else {
            result[i] = nums[right] * nums[right];
            right--;
        }
        i--;
    }
    return result;
}
```

### 189、旋转数组

用一个新的数组存储移动后的元素，最后再拷贝过来。

```c
void rotate(int* nums, int numsSize, int k){
    int *new_nums = (int *)malloc(sizeof(int) * numsSize);
    for (int i = 0; i < numsSize; i++) {
        new_nums[i] = 
        	nums[(i + (k / numsSize + 1) * numsSize - k) % numsSize];
    // 因为k可能大于numsSize，如果直接用k * numsSize又会溢出
    }
    for (int i = 0; i < numsSize; i++) {
        nums[i] = new_nums[i];
    }
    free(new_nums);
}
```

把上面的思路捋一下，其实就是双指针，一个是i，一个是i - k。换成i和i + k更好操作一些。

```c
void rotate(int* nums, int numsSize, int k){
    int *new_nums = (int *)malloc(sizeof(int) * numsSize);
    int left = 0, right = k;
    for (left = 0; left < numsSize; left++) {
        new_nums[right % numsSize] = nums[left];
        right++;
    }
    for (int i = 0; i < numsSize; i++) {
        nums[i] = new_nums[i];
    }
}
```

那么，有没有什么办法可以让空间复杂度是O（1）呢？举个栗子。

![image-20211028145901624](C:\Users\pikachu\AppData\Roaming\Typora\typora-user-images\image-20211028145901624.png)

```c
void reverse(int *nums, int s, int t)
{
    while (s < t) {
        int temp = nums[s];
        nums[s] = nums[t];
        nums[t] = temp;
        s++;
        t--;
    }
}
void rotate(int* nums, int numsSize, int k){
    k = k % numsSize;
    reverse(nums, 0, numsSize - 1);
    reverse(nums, 0, k - 1);
    reverse(nums, k, numsSize - 1);
}
```
`i`用`i - k`代替，`i - k`用`i - 2k`，一直循环。遍历所有没有代替过的值。
```c

int gcd(int a, int b) {
    return b ? gcd(b, a % b) : a;
}

void swap(int* a, int* b) {
    int t = *a;
    *a = *b, *b = t;
}

void rotate(int* nums, int numsSize, int k) {
    k = k % numsSize;
    int count = gcd(k, numsSize);
    for (int start = 0; start < count; ++start) {
        int current = start;
        int prev = nums[start];
        do {
            int next = (current + k) % numsSize;
            swap(&nums[next], &prev);
            current = next;
        } while (start != current);
    }
}
```

