### 231、2的幂

左移右移

```c
bool isPowerOfTwo(int n){
    int x = 1;
    while (x <= n) {
        if (x == n)
            return true;
        // 避免越界
        else if (x <= INT_MAX / 2)
            x = x << 1;
        else 
            break;
    }
    return false;
}
```

我们知道，2的幂的二进制表示其本身只有一个1，我们可以这样来入手。比如`n=0010000`，那么`n-1=0001111`，我们取与运算结果就是0。

```c
bool isPowerOfTwo(int n){
    return n > 0 && (n & (n - 1)) == 0;
}
```

递归做法

```c
bool isPowerOfTwo(int n){
    if (n <= 0)
     return false;
    if (n == 1)
     return true;
    else if(n % 2 != 0)
     return false;
    return isPowerOfTwo(n / 2);
}
```

取反加一（或者直接-n）

```c
bool isPowerOfTwo(int n){
    return n > 0 && (n & (~n + 1)) == n;
}
```

### 191、位1的个数

左右移

```c
int hammingWeight(uint32_t n) {
    int count = 0;
    while (n > 0) {
        if (n % 2 == 1)
            count++;
        n = n >> 1;
    }
    return count;
}
```

自己在纸上写写 `n & (n - 1)`

```c
int hammingWeight(uint32_t n) {
    int count = 0;
    while (n > 0) {
        count++;
        n = n & (n - 1);
    }
    return count;
}
```

