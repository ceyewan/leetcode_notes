> 在用C语言刷力扣的时候，我们经常会看到这两个东西，`int* returnSize` 和 `int** returnColumnSizes`。连题目都看不懂，更何况是做题呢？我也是查找了网上零零碎碎的资料和做了蛮多题之后，终于想明白了。遂整理成此文，希望能帮到后来者。个人理解，如有不足，烦请斧正。

首先我们来看一道题，力扣的第 15 题，[三数之和](https://leetcode-cn.com/problems/3sum/)。这是笔者第一次接触到这两个东西的题目。

### 三数之和

#### 题干：

> 给你一个包含 n 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。
>
> 注意：答案中不可以包含重复的三元组。
>

#### 示例：

```c
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
    
输入：nums = []
输出：[]
    
输入：nums = [0]
输出：[]
```

#### 提示：

- `0 <= nums.length <= 3000`
- `-105 <= nums[i] <= 105`

#### 默认的代码模板

```c
/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
int** threeSum(int* nums, int numsSize, int* returnSize, int** returnColumnSizes){

}
```

### 分析

题目的意思很明确了，就是这个数组中找到 3 个数，保证 3 个数的和恰好为 `target` 。我们看默认的代码模板，返回值是一个二阶指针，结合示例，我们可以知道这里需要返回的是一个二维数组。传入的第一个参数 `int *nums`也很容易理解，就是输入的数组；第二个参数 `int numsSize` 也容易理解，是数组的长度；那么第三个参数、第四个参数呢？我要处理的是数组，你把数组给我就行了，还多给两个不明不白的东西干啥？

我们看看代码模板的注释，复制到谷歌翻译（大佬可以跳过这一步）。我们可以看到，三句话的意思是：

- 返回大小为 `*returnSize` 的数组数组。 
- 数组的大小作为 `*returnColumnSizes` 数组返回。 
- 注意：返回的数组和 `*columnSizes` 数组都必须被 `malloced`，假设调用者调用 `free()`。

第一句话的数组数组声明意思呢？数组里面的元素是数组，其实就是二维数组。我们还是很容易理解 `returnSize` 其实就是一个指针的，那么 `*returnSize` 就代表返回大小，这里的返回大小就是我们要返回的二维数组的行数。还是很容易理解的，毕竟我们不告诉编译器我们的二维数组有多少行，编译器又从何得知呢？

再看第二句话，数组的大小作为  `*returnColumnSizes` 数组返回。说明 `*returnColumnSizes` 是一个数组。那么数组的大小是什么意思？前面我们不是说了数组的大小是 `*returnSize` 吗？怎么又变成 `*returnColumnSizes` 了？没有搞错吧？确实没有搞错，二维数组除了行还有列啊！那么新的问题又来了，列不是就是3吗？用一个数来表示不就行了？干嘛要用一个数组？这个我只能说，万一呢？万一每一行的长度不一样呢？比如第一行 3 个元素，第二行 2 个元素。力扣就是有这么“严谨”，要求你告诉它每一行有多少个元素。那么你又会有问题了，一个数组，不应该和 `nums` 一样用指针传入吗？怎么这里是二阶指针？我们先看第三句话。

第三句话，返回的数组和 `*columnSizes` 数组都必须被 `malloced` ，假设调用者调用 `free()` ，重点在于：  `*columnSizes` 数组都必须被 `malloced`  （ `*columnSizes` 就是 `*returnColumnSizes`）。这说明 `*returnColumnSizes`还没初始化呢，还不是一个数组呢。这也很容易理解， `*returnColumnSizes` 是用来装我们返回的二维数组每一行有多少个元素的（即列数），如果返回 5 行，则 `*returnColumnSizes` 的长度也得是 5 ， 比如说第一个元素是 3 表示二维数组的第一行有 3 个数，第二个元素是 4 表示二维数组的第二行有 4 个数。别人怎么知道你的二维数组有多少行？自然也就不知道应该初始化 `*returnColumnSizes` 数组为多大了，这一切还得你来。你 `malloc` 一个数组，自然就会得到一个指向数组的一阶指针，`main` 函数传进来一个二阶指针指向你这个一阶指针，这样它就能找到你返回的列数组了。

好了，或许你还没懂，不过没关系，我们现在来看看我猜测的 `main` 函数是什么样子的。

```c
int main()
{
    int nums[6] = {-1, 0, 1, 2, -1, -4};
    int numsSize = 6;
    int returnSize;// 表示返回的二维数组的行数
    int **returnColumnSize;// 指向列数组指针的指针
    // 注意：列数组在哪我们无从得知，也不需要知道，
    // 我们只要知道有个一阶指针指向它就行了，我把它叫做列数组指针
    int **ans = threeSum(nums, numsSize, &returnSize, returnColumnSizes);
    // &returnSize 引用传值
    // 最后一个参数我认为也可以是 int *returnColumnSize，然后传入&returnColumnSizes，效果是一样的
    for (int i = 0; i < (*returnSize); i++) {
        for (int j = 0; j < (*returnColumnSize)[i]; j++)
            printf("%d", ans[i][j]);
        printf("\n");
    }
    return 0;
}
```

我们再来看看 `threeSum` 函数里面我们需要做的是什么事。

```c
int **threeSum(int *nums, int numsSize, int *returnSize, int **returnColumnSizes)
{
    // ans是二维数组，先假设有100000个答案，准备有100000个一阶指针的空间先
    // 每个一阶指针可以指向一个数组，即一个答案
    int **ans == (int **)malloc(sizeof(int *) * 100000);
    // returnSize是一阶指针， *returnSize是值
    *returnSize = 0;// 答案组数为0
    while (找到一组答案) {
        // malloc一个数组存储答案
        int *temp = (int *)malloc(sizeof(int) * 3);// 3个数嘛，3个int
        f(把答案写入temp);
        ans[(*returnSize)] = temp;// 这两个都是一阶指针
        (*returnSize)++；
    }
    // returnColumnSizes是二阶指针， (*returnColumnSizes)是一阶指针
    // 有(*returnSize)行就malloc(*returnSize)个元素
    (*returnColumnSizes) = (int *)malloc(sizeof(int) * (*returnSize));
    // 本题中每一个元素都是3
    for (int i = 0; i < (*returnSize); i++) {
        (*returnColumnSizes)[i] = 3;
    }
}
// 我们虽然ans数组malloc了100000个空间，但是其实main函数只会用到前（*returnSize）个，多出来的就多出来吧
```

### 题解

三重循环暴力法就不写了，这里贴一个双指针的，具体解法力扣的题解讲的比我清楚得多。

```c
# define MAX 100000	// 答案的个数，当然也能用动态扩容
int comp(const void *a, const void *b)
{
    return *(int *)a > *(int *)b ? 1 : 0;
}
int** threeSum(int* nums, int numsSize, int* returnSize, int** returnColumnSizes){
    qsort(nums, numsSize, sizeof(int), comp);
    int **ans = (int **)malloc(sizeof(int *) * MAX);
    *returnSize = 0;
    if (numsSize < 3)   return ans;
    int i = 0;
    while (i < numsSize - 2) {
        int left = i + 1, right = numsSize - 1;
        while (left < right) {
            int sum = nums[left] + nums[right] + nums[i];
            if (sum == 0) {
                int *temp = (int *)malloc(sizeof(int) * 3);
                temp[0] = nums[i];
                temp[1] = nums[left];
                temp[2] = nums[right];
                ans[(*returnSize)++] = temp;
            }
            // 这个地方不能是else if，因为总得改变当前的状态，如果是else if，那么找到了一个答案之后就永远不变了，这是不行的
            if (sum < 0)
                // 小了，就改变left。跳过重复的同时避免出界
                while (left < numsSize - 1 && nums[left] == nums[++left]);
            else
                // right跳过重复的
                while (right > 0 && nums[right] == nums[--right]);
        }
        // 第一个数字i，同样避免重复和越界
        while(i < numsSize - 1 && nums[i] == nums[++i]);
    }
    *returnColumnSizes = (int *)malloc(sizeof(int) * (*returnSize));
    for (int i = 0; i < (*returnSize); i++)
        (*returnColumnSizes)[i] = 3;
    return ans;
}
```

### 小结

希望本文能帮到你，如果还有不明白的地方，欢迎和我交流。

