

### 190、颠倒二进制位

```c
uint32_t reverseBits(uint32_t n) {
    uint32_t x = 0;
    int i = 0;
    while (i < 32) {
        x = x << 1;
        x |= n % 2;// 实际上是+=，用或运算快
        n =  n >> 1;
        i++;
    }
    return x;
}
```

### 136、只出现一次的数字

```c
int singleNumber(int* nums, int numsSize){
    int x = 0;
    for (int i = 0; i < numsSize; i++)
        x ^= nums[i];// 与或运算，具体参考T260，已经写过了
    return x;
}
```

