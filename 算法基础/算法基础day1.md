### 34、在排序数组中查找元素的第一个和最后一个位置

```c
int* searchRange(int* nums, int numsSize, int target, int* returnSize){
    int *result = (int *)malloc(sizeof(int) * 2);
    *returnSize = 2;
    result[0] = result[1] = -1;
    int left = 0, right = numsSize - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            left = right = mid;
            while (--left > -1 && nums[left] == target);
            while (++right < numsSize && nums[right] == target);
            result[0] = left + 1;
            result[1] = right - 1;
            break;
        }
        else if (nums[mid] < target) {
            left = mid + 1;
        }
        else {
            right = mid - 1;
        }
    }
    return result;
}
```

### 33、搜索旋转排序数组

```c
int b_search(int *nums, int s, int t, int target)
{
    int low = s, high = t;
    int mid;
    while (low <= high) {
        mid = low + ((high - low) >> 1);
        if (nums[mid] == target)
            return mid;
        else if (nums[mid] > target)
            high = mid - 1;
        else
            low = mid + 1;
    }
    return -1;
}
int search(int* nums, int numsSize, int target){
    int low = 0, high = numsSize - 1;
    while (low <= high) {
        int mid = low + ((high - low) >> 1);
        if (nums[mid] == target)
            return mid;
        else if (mid == numsSize - 1 || nums[mid] > nums[mid + 1])
            break;
        else if (nums[mid] >= nums[0])
            low = mid + 1;
        else
            high = mid - 1;
    }
    int result = -1;
    if (mid == numsSize - 1)
        result = b_search(nums, 0, numsSize - 1, target);
    else if (nums[0] <= target)
        result = b_search(nums, 0, mid, target);
    else
        result = b_search(nums, mid + 1, numsSize - 1, target);
    return result;    
}
```

### 74、搜索二维矩阵

人生苦短，我用暴力法。

```c
bool searchMatrix(int** matrix, int matrixSize, int* matrixColSize, int target){
    int m = matrixSize, n = matrixColSize[0];
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (matrix[i][j] == target)
                return true;
        }
    }   
    return false;
}
```

首先在全部行的第一个元素上进行二分，确定是在哪一行，然后在那一行再一次二分，确定列数。

```c
bool searchMatrix(int** matrix, int matrixSize, int* matrixColSize, int target){
    int m = matrixSize, n = matrixColSize[0];
    // 不在这个范围内，单独处理
    if (target < matrix[0][0] || target > matrix[m - 1][n - 1])
        return false;
    int left = 0, right = m - 1;
    int line = 0;
    while (left <= right) {
        int mid = (left + right) / 2;
        if (matrix[mid][0] == target)
            return true;
        else if (matrix[mid][0] > target)
            right = mid - 1;
        // mid是最后一行了，那么肯定就是在这一行（因为target一定在矩阵范围内）
        // 或者就是满足后面的条件，能确定target在第mid行
        else if (mid == m - 1 || (matrix[mid][0] < target && matrix[mid + 1][0] > target)) {
            line = mid;
            break;
        } else
            left = mid + 1;
    }
    left = 0; right = n - 1;
    while (left <= right) {
        int mid = (left + right) / 2;
        if (matrix[line][mid] == target)
            return true;
        else if (matrix[line][mid] > target)
            right = mid - 1;
        else
            left = mid + 1;
    }
    return false;
}
```

整体上直接二分，把二维直接看作一维。

```c
bool searchMatrix(int** matrix, int matrixSize, int* matrixColSize, int target){
    int m = matrixSize, n = matrixColSize[0];
    if (target < matrix[0][0] || target > matrix[m - 1][n - 1])
        return false;
    int left = 0, right = m * n - 1;
    while (left <= right) {
        int mid = (left + right) / 2;
        if (matrix[mid / n][mid % n] == target)
            return true;
        else if (matrix[mid / n][mid % n] > target)
            right = mid - 1;
        else
            left = mid + 1;
    }
    return false;
}
```

前两题写过了，也很简单，所以就贴个答案就好了。
