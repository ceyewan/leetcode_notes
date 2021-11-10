### 153、寻找旋转排序数组的最小值

和33题一样，比33题简单。

```c
int findMin(int* nums, int numsSize){
    int left = 0; int right = numsSize - 1;
    while (left <= right) {
        int mid = (left + right) / 2;
        // 第一个条件防止越界，后一个满足条件
        if (mid > 0 && nums[mid] < nums[mid - 1])
            return nums[mid];
        else if (nums[mid] > nums[0] || mid == 0)
            left = mid + 1;
        else
            right = mid - 1;
    }
    // 只有mid=0的时候前面才不会返回
    return nums[0];
}
```

### 162、寻找峰值

```c
int findPeakElement(int* nums, int numsSize){
    if (numsSize == 1 || nums[0] > nums[1])
        return 0;   
    if (nums[numsSize - 1] > nums[numsSize - 2])
        return numsSize - 1;
    // 基于前面的考虑，下面我们就不用考虑越界了
    int left = 1; int right = numsSize - 2;
    while (left <= right) {
        int mid = (left + right) / 2;
        if (nums[mid] > nums[mid - 1] && nums[mid] > nums[mid + 1])
            return mid;
        // 可以确定前面一定有峰值
        else if (nums[mid] < nums[mid - 1])
            right = mid - 1;
        else
            left = mid + 1;
    }
    // 没找到，就返回-1，但是实际上一定会有的
    return -1;
}
```

