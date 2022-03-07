

### 139、单词拆分

>给你一个字符串 s 和一个字符串列表 wordDict 作为字典，判定 s 是否可以由空格拆分为一个或多个在字典中出现的单词。
>
>说明：拆分时可以重复使用字典中的单词。

```c
bool wordBreak(char * s, char ** wordDict, int wordDictSize){
    int length = strlen(s);
    int dp[length + 1];
    memset(dp, 0, sizeof(dp));
    dp[0] = 1;	// dp[i]表示第i个字母之前是匹配的
    for (int i = 1; i <= length; i++) {
        for (int j = 0; j < wordDictSize; j++) {
            int temp_length = strlen(wordDict[j]);
            int k = i - temp_length;
            if (k < 0)  continue;
            if (strncmp(s + k, wordDict[j], temp_length) == 0 && dp[i] == 0)
                dp[i] = dp[k];
        }
    }
    return dp[length];
}
```

原本想回溯法来做的，但是没办法，超时了。和上面的区别就是前面匹配了的东西，没有去记录下来，重复算了好多次。

```c
bool huisu(char **wordDict, int wordDictSize, char *s, int length, int count)
{
    if (count == length) {
        return true;
    }
    bool judge = false;
    for (int i = 0; i < wordDictSize; i++) {
        int temp_length = strlen(wordDict[i]);
        char *temp = (char *)malloc(sizeof(char) * (temp_length + 1));
        strncpy(temp, s + count, temp_length);
        temp[temp_length] = '\0';
        if (strcmp(wordDict[i], temp) == 0) {
            judge = huisu(wordDict, wordDictSize, s, length, count + temp_length);
            if (judge) {
                return true;
            }
        }
    }
    return false;
}
bool wordBreak(char * s, char ** wordDict, int wordDictSize){
    int length = strlen(s);
    return huisu(wordDict, wordDictSize, s, length, 0);
}
```

### 91、解码方法

>一条包含字母 A-Z 的消息通过以下映射进行了 编码 ：
>
>```
>'A' -> 1
>'B' -> 2
>...
>'Z' -> 26
>```
>
>要 解码 已编码的消息，所有数字必须基于上述映射的方法，反向映射回字母（可能有多种方法）。例如，"11106" 可以映射为：
>
>```
>"AAJF" ，将消息分组为 (1 1 10 6)
>"KJF" ，将消息分组为 (11 10 6)
>```
>
>注意，消息不能分组为  (1 11 06) ，因为 "06" 不能映射为 "F" ，这是由于 "6" 和 "06" 在映射中并不等价。
>
>给你一个只含数字的 非空 字符串 s ，请计算并返回 解码 方法的 总数 。
>
>题目数据保证答案肯定是一个 32 位 的整数。
>

```c
int numDecodings(char * s){
    int length = strlen(s);
    int dp[length];
    memset(dp, 0, sizeof(dp));
    if (s[0] == '0')
        return 0;
    dp[0] = 1;
    for (int i = 1; i < length; i++) {
        // 如果是0且可以和前面的匹配，则必须匹配
        if (s[i] == '0' && ((s[i - 1] - '0') * 10 + s[i] - '0' <= 26 && (s[i - 1] - '0') * 10 + s[i] - '0' >= 10)) {
            if (i == 1)
                dp[i] = 1;
            else 
                dp[i] = dp[i - 2];
        }
        // 可以和前一个匹配，那么可以和之匹配或者不和前面的匹配
        else if ((s[i - 1] - '0') * 10 + s[i] - '0' <= 26 && (s[i - 1] - '0') * 10 + s[i] - '0' >= 10) {
            if (i == 1)
                dp[i] = 2;
            else 
                dp[i] = dp[i - 1] + dp[i - 2];
        }
        // 如果是0还不能和前面的匹配
        else if (s[i] == '0')
            return 0;
        else
            dp[i] = dp[i - 1];
    }
    return dp[length - 1];
}
```

