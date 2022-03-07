### 题目

[实现`strStr`](https://leetcode-cn.com/problems/implement-strstr/)

>实现 `strStr()` 函数。
>
>给你两个字符串 `haystack`和 `needle` ，请你在 `haystack `字符串中找出 `needle` 字符串出现的第一个位置（下标从 0 开始）。如果不存在，则返回  -1 。
>
>说明：
>
>* 当 `needle` 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。
>
>* 对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与 C 语言的 `strstr()` 以及 Java 的 `indexOf() `定义相符。

### 思路一

遍历`haystack` ，如果有个字符和`needle`的第一个字符一样，我们就去比较他们是不是匹配，如果匹配则返回，不匹配就继续遍历。
```c
int match(char *haystack, char *needle, int n)
{
    int i = 0;
    for (i = 0; haystack[n + i] != '\0' && needle[i] != '\0'; i++)
        if (haystack[n + i] != needle[i])
            return -1;
    if (needle[i] != '\0')
        return -1;
    return n;
}
int strStr(char * haystack, char * needle){
  	int m = strlen(haystack), n = strlen(needle);
    if (n == 0) return 0;
    int judge = -1;
    for (int i = 0; i + n < m; i++) {// 这个地方不用i < n,优化一点，可以避免超时
        if (haystack[i] == needle[0])
            judge = match(haystack, needle, i);
            if (judge >= 0)
                return judge;
    }
    return judge;
}
```

### 思路二

大名鼎鼎的`KMP`字符串模式匹配算法，首先就是求`next[]`数组，然后通过 `next[]` 数组进行匹配滑动。`next[]`数组表示的是模式串的最长前缀匹配长度和后缀匹配长度。比如`ababae`，第一个我们默认设置为0，第二个因为和第一个不一样，所以也是0，第三个的话，就是1；第4个的话，就是2（ab和ab）。怎么求`next[]`呢？我们刚刚说了，第四个是2，即`next[3] = 2`，那么我们看`s[4]`和`s[2]`匹不匹配，匹配的话匹配的长度就加1，所以`next[4] = 3`。`s[5]`和`s[3]`不匹配，就回退，`j = next[j - 1]`，再来试。

```c
int strStr(char* haystack, char* needle) {
    int n = strlen(haystack), m = strlen(needle);
    if (m == 0)
        return 0;
    int next[m];
    next[0] = 0;
    int j = 0;
    for (int i = 1; i < m; i++) {
        // 如果不相等，那么就回退
        while (j > 0 && needle[i] != needle[j]) {
            j = next[j - 1];
        }
        // 相等，那么就加一
        if (needle[i] == needle[j]) {
            j++;
        }
        next[i] = j;
    }
    for (int i = 0, j = 0; i < n; i++) {
        while (j > 0 && haystack[i] != needle[j]) {
            j = next[j - 1];
        }
        if (haystack[i] == needle[j]) {
            j++;
        }
        if (j == m) {
            return i - m + 1;
        }
    }
    return -1;
}
```

