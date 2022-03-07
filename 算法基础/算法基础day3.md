### 84、删除链表中的重复元素

注意要释放删除节点的空间，虽然不释放也没什么关系，也能过。

```c
struct ListNode* deleteDuplicates(struct ListNode* head){
    if (head == NULL || head->next == NULL)
        return head;
    // 哑节点
    struct ListNode *dummy = (struct ListNode *)malloc(sizeof(struct ListNode));
    dummy->next = head;
    struct ListNode *pre = dummy, *p = head;
    while (p != NULL && p->next != NULL) {
        struct ListNode *q = p->next;
        if (p->val == q->val) {
            int temp = p->val;
            free(p);
            while (q != NULL && temp == q->val) {
                struct ListNode *r = q->next;
                free(q);
                q = r;
            }
            pre->next = q;
            p = pre->next;
        }
        else {
            pre = pre->next;
            p = pre->next;
        }
    }
    head = dummy->next;
    free(dummy);
    return head;
}   
```

### 15、三数之和

```c
# define MAX 100000	// 答案的个数
int comp(const void *a, const void *b)
{
    return *(int *)a > *(int *)b ? 1 : 0;
}
int** threeSum(int* nums, int numsSize, int* returnSize, int** returnColumnSizes){
    qsort(nums, numsSize, sizeof(int), comp);
    int **ans = (int **)malloc(sizeof(int *) * MAX);
    *returnSize = 0;
    *returnColumnSizes = (int *)malloc(sizeof(int) * MAX);
    for (int i = 0; i < MAX; i++)
        (*returnColumnSizes)[i] = 3;
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
    return ans;
}
```

前面固然没错，但是也太烦了吧。尤其是那个MAX，根本不知道有多大，不如采用动态扩容，这样空间比较划得来。

```c

int comp(const void *a, const void *b)
{
    return *(int *)a > *(int *)b ? 1 : 0;
}
int** threeSum(int* nums, int numsSize, int* returnSize, int** returnColumnSizes){
    qsort(nums, numsSize, sizeof(int), comp);
    int count = 0;// 计数，答案的个数
    int basicSize = 8;// 先给8个的空间
    int **ans = (int **)malloc(sizeof(int *) * basicSize);
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
                // 装满了，动态扩容到之前的两倍
                if (count == basicSize) {
                    basicSize = basicSize << 1;
                    // 拷贝一份ans，空间乘以2
                    ans = (int **)realloc(ans, sizeof(int *) * basicSize);
                }
                // 写入答案
                ans[count++] = temp;
            }
            if (sum < 0)
                while (left < numsSize - 1 && nums[left] == nums[++left]);
            else
                while (right > 0 && nums[right] == nums[--right]);
        }
        while(i < numsSize - 1 && nums[i] == nums[++i]);
    }
    // 需要返回的一些数据
    *returnSize = count;
    *returnColumnSizes = (int *)malloc(sizeof(int) * count);
    for (int i = 0; i < count; i++) 
        (*returnColumnSizes)[i] = 3;
    return ans;
}
```

