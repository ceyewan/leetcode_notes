### 844、比较含退格的字符串

利用栈来保存最终的字符串，如果是字母就入栈；如果是#且栈不为空就出栈一个。最终比较两个栈。

```c
#define MAX 200
char stack1[MAX];
char stack2[MAX];
bool backspaceCompare(char * s, char * t){
    int top1 = -1, top2 = -1;
    for (int i = 0; s[i] != '\0'; i++) {
        if (s[i] != '#')
            stack1[++top1] = s[i];
        else {
            if (top1 >= 0)
                top1--;
        }
    }
    for (int i = 0; t[i] != '\0'; i++) {
        if (t[i] != '#')
            stack2[++top2] = t[i];
        else {
            if (top2 >= 0)
                top2--;
        }
    }
    bool judge = true;
    if (top1 == top2) {
        for (int i = 0; i <= top1; i++)
            if (stack1[i] != stack2[i])
                judge = false;
    }
    else 
        judge = false;
    return judge;
}
```

我们可以用它自己来当作栈，slow表示栈顶，fast遍历字符串。（内存消耗反而更高了，笑死）

```c
bool backspaceCompare(char * s, char * t){
    // 处理s
    int fast1 = 0, slow1 = -1;
    while (s[fast1++] != '\0') {
        if (s[fast1] == '#') {
            if (slow1 >= 0)
                slow1--;
        }
        else
            s[++slow1] = s[fast1];
    }
    s[++slow1] = '\0';
    // 处理t
    int fast2 = 0, slow2 = -1;
    while (t[fast2++] != '\0') {
        if (t[fast2] == '#') {
            if (slow2 >= 0)
                slow2--;
        }
        else
            t[++slow2] = t[fast2];
    }
    t[++slow2] = '\0';
    // 判断
    if (slow1 != slow2)
        return false;
    for (int i = 0; i < slow1; i++)
        if (s[i] != t[i])
            return false;
    return true;
}
```

### 11、乘最多水的容器

从两边选择，求出容积。然后改变矮的那根，而且要改变之后至少比原来高。

```c
int maxArea(int* height, int heightSize){
    int left = 0, right = heightSize - 1;
    int left_height = 0, right_height = 0;
    int sum = 0, max = 0;
    while (left < right) {
        left_height = height[left];
        right_height = height[right];
        sum = (right - left) * fmin(left_height, right_height);
        max = max > sum ? max : sum;
        if (left_height < right_height)
            // 避免越界， 注意后面有个分号
            while (left < heightSize - 1 && left_height >= height[++left]);
        else
            while (right > 0 && right_height >= height[--right]);
    }
    return max;
}
```

### 986 区间列表的交集

把两个表覆盖到的东西放到一个数组里，然后遍历这个数组，得到重叠的部分。尼玛，于是超时了。

```c
int** intervalIntersection(int** firstList, int firstListSize, int* firstListColSize, int** secondList, int secondListSize, int* secondListColSize, int* returnSize, int** returnColumnSizes){
    int count = 0;
    int basicSize = 8;
    int **ans = (int **)malloc(sizeof(int *) * basicSize);
    if (firstListSize == 0 || secondListSize == 0) {
        *returnSize = 0;
        return ans;
    }
    int start = fmin(firstList[0][0], secondList[0][0]);
    //很显然每列2个
    int end = fmax(firstList[firstListSize - 1][1], secondList[secondListSize - 1][1]);
    int *nums = (int *)malloc(sizeof(int) * (end - start + 1));
    memset(nums, 0, sizeof(int) * (end - start + 1));
    for (int i = 0; i < firstListSize; i++) {
        // 因为我们只考虑了整数，但是其实最末尾那个数后面是没有了，其他的数后面还有
        // 所以用1和2分开
        for (int j = firstList[i][0]; j < firstList[i][1]; j++)
            nums[j - start] += 2;
        nums[firstList[i][1] - start]++;
    }
    for (int i = 0; i < secondListSize; i++) {
        for (int j = secondList[i][0]; j < secondList[i][1]; j++)
            nums[j - start] += 2;
        nums[secondList[i][1] - start]++;
    }
    // 遍历整个数组
    int i = 0;
    while( i < end - start + 1) {
        // 遇到4开头的就是重叠部分了
        if (nums[i] == 4) {
            int j = i;
            while (j < end - start + 1 && nums[++j] == 4);
            // 不满足=4，那么就是3或者2，也就意味着结尾了，肯定不会直接跳到0的
            int *temp = (int *)malloc(sizeof(int) * 2);
            temp[0] = start + i;
            temp[1] = start + j;
            if (count == basicSize) {
                basicSize *= 2;
                ans = (int **)realloc(ans, sizeof(int *) * basicSize);
            }
            ans[count++] = temp;
            i = j + 1;
        }
        // 如果是3开头，说明某一个是最后了，那么就是个点集
        else if (nums[i] == 3) {
            int *temp = (int *)malloc(sizeof(int) * 2);
            temp[0] = start + i;
            temp[1] = start + i;
            if (count == basicSize) {
                basicSize *= 2;
                ans = (int **)realloc(ans, sizeof(int *) * basicSize);
            }
            ans[count++] = temp;
            i++;
        }
        else 
            i++;
    }
    *returnSize = count;
    *returnColumnSizes = (int *)malloc(sizeof(int) * count);
    for (int i = 0; i < count; i++)
        (*returnColumnSizes)[i] = 2;
    return ans;
}
```

双指针做法， `i`和`j`是两个指针，并且采用动态扩容求答案。

```c
int** intervalIntersection(int** firstList, int firstListSize, int* firstListColSize, int** secondList, int secondListSize, int* secondListColSize, int* returnSize, int** returnColumnSizes){
    *returnSize = 0;
    int basicSize = 8;
    int **ans = (int **)malloc(sizeof(int *) * basicSize);
    int m = firstListSize, n = secondListSize;
    if (m == 0 || n == 0) {
        return ans;
    }
    int i = 0, j = 0;
    while (i < m && j < n) {
        // first最大的数小于second最小的数，则first行数增加
        if (firstList[i][1] < secondList[j][0])
            i++;
        // first最小的数大于second最大的数，则second行数增加
        else if (secondList[j][1] < firstList[i][0])
            j++;
        // 一定有重合部分
        else {
            int min = fmax(firstList[i][0], secondList[j][0]);
            int max = fmin(firstList[i][1], secondList[j][1]);
            int *temp = (int *)malloc(sizeof(int) * 2);
            temp[0] = min;
            temp[1] = max;
            if (*returnSize == basicSize) {
                basicSize *= 2;
                ans = (int **)realloc(ans, sizeof(int *) * basicSize);
            }
            ans[(*returnSize)++] = temp;
            // 最大值小的那个需要增加
            if (firstList[i][1] < secondList[j][1])
                i++;
            else 
                j++;
        }
    }
    // 这个可以和ans一起操作，减少一点点时间。2n->n
    *returnColumnSizes = (int *)malloc(sizeof(int) * (*returnSize));
    for (int i = 0; i < *returnSize; i++)
        (*returnColumnSizes)[i] = 2;
    return ans;
}
```

