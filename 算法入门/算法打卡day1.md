### 704、二分查找

```c
int my_bsearch(int* nums, int left, int right, int target)
{
    if (left > right)
        return -1;
    int mid = left + (right - left) / 2;
    if (nums[mid] == target) 
        return mid;
    else if (nums[mid] > target) {
        return my_bsearch(nums, left, mid - 1, target);
    }
    else {
        return my_bsearch(nums, mid + 1, right, target);
    }
}
int search(int* nums, int numsSize, int target){
    return my_bsearch(nums, 0, numsSize - 1, target);
}
```

### 278、第一个错误的版本

```c
int firstBadVersion(int n) {
    int left = 1, right = n;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (isBadVersion(mid)) {
            right = mid - 1;
        }
        else {
            left = mid + 1;
        }
    }
    if (isBadVersion(left))
        return left;
    else 
        return left + 1;
}
```

### 35、搜索插入位置

```c
int searchInsert(int* nums, int numsSize, int target){
    int left = 0, right = numsSize - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target)
            return mid;
        else if (nums[mid] < target) {
            left = mid + 1;
        }
        else {
            right = mid - 1;
        }
    }  
    return left;
}
```

