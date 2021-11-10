### 283、移动零

顺序遍历整个数组，遇到0就count加一，遇到不是0的就往前移动count位（count>0)，然后把这一位设置为0。这样所有的就处理完了。

```c
void moveZeroes(int* nums, int numsSize){
    int count = 0;
    for (int i = 0; i < numsSize; i++) {
        if (nums[i] == 0) {
            count++;
        }
        else if (count > 0) {// 如果没有这一句，那么第一个不是0的就会被设置为0，并且没有后面的值来把它覆盖的话，就会出错。
            nums[i - count] = nums[i];
            nums[i] = 0;
        }
    }
}
```

快慢指针，慢指针一次前进一格，快指针跳过所有的0。

```c
void moveZeroes(int* nums, int numsSize){
    int fast = 0, slow = 0;
    while (fast < numsSize && nums[fast] == 0) {
        fast++;
    }
    while (fast < numsSize) {
        nums[slow] = nums[fast];
        if (fast > slow)
            nums[fast] = 0;
        slow++;
        fast++;
        while (fast < numsSize && nums[fast] == 0) {
            fast++;
        }
    }
}
```

### 167两数之和Ⅱ-输入有序数组

我不明白，这不就是第一题，有任何区别吗？甚至都是排好序的数组？？！

```c
int* twoSum(int* numbers, int numbersSize, int target, int* returnSize){
    int *result = (int *)malloc(sizeof(int) * 2);
    int left = 0, right = numbersSize - 1;
    while (left < right) {
        if (numbers[left] + numbers[right] == target) {
            *returnSize = 2;
            result[0] = left + 1;
            result[1] = right + 1;
            break;
        }
        else if (numbers[left] + numbers[right] < target) {
            left++;
        }
        else {
            right--;
        }
    }
    return result;
}
```

