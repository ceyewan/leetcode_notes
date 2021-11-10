### 3、无重复字符的最长子串

滑动窗口，两次循环就可以了。时间复杂度是`n^2`。

```c
int lengthOfLongestSubstring(char * s){
    int sign[128];//标记是否有了某个字符
    int max = 0;
    for (int i = 0; s[i] !=  '\0'; i++) {
        memset(sign, 0, sizeof(int) * 128);
        int count = 0;
        for (int j = i; s[j] != '\0'; j++) {
            if (sign[s[j]] == 0) {
                sign[s[j]]++;
                count++;
            } else {
                break;
            }
            max = max > count ? max : count;
        }
    }
    return max;
}
```

左右指针滑动，左指针每次动一下，右指针动到重复的地方。

```c
int lengthOfLongestSubstring(char * s){
    int sign[128];
    memset(sign, 0, sizeof(int) * 128);
    int max = 0;
    int left = 0, right = 0;
    while (s[right] != '\0') {
        // 只要是没有标记的，就入队（滑动队列），然后标记
        while (s[right] != '\0' && sign[s[right]] == 0) {
            sign[s[right]] = 1;
            right++;
        }
        max = max > (right - left) ? max : (right - left);
        // 最后一个出队，取消标记
        sign[s[left]] = 0;
        left++;
    }
    return max;
}
```
左指针每次移动一个位置干嘛，要移动到和s[right]重复的位置，这样更快。
```c
int lengthOfLongestSubstring(char * s){
    int i, j, index[128] = {0}, count = 0, max = 0, start = 0;
    for(i = 0; s[i] != '\0'; i++) {
        if(index[s[i]] > start){
            count = i - start;
            if(count > max)
                max = count;
            start = index[s[i]];
        }
    index[s[i]] = i + 1;
    }
    count = i - start;
    return count > max ? count : max;
}
```

### 567、字符串的排列

哈希算法，时间复杂度是n*m，勉强通过测试。

```c
struct my_struct {
    int id;
    int count;
    UT_hash_handle hh;
};
bool isequal(char *s2, int len, struct my_struct *users)
{
    struct my_struct *s;
    int num = -1;
    // 哈希匹配
    for (int i = 0; i < len; i++) {
        int a = s2[i];
        HASH_FIND_INT(users, &a, s);
        if (s != NULL && s->count > 0) {
            s->count--;
        }
        else {
            num = i;// 标记不匹配的那个值
            break;
        }
    }
    if (num == -1)
        return true;
    // 恢复哈希，使之不影响下一次匹配
    for (int i = 0; i < num; i++) {
        int a = s2[i];
        HASH_FIND_INT(users, &a, s);
        s->count++;
    }
    return false;
}
bool checkInclusion(char * s1, char * s2){
    struct my_struct *users = NULL;
    int len = strlen(s1), lens = strlen(s2);
    if (len > lens)
        return false;
    struct my_struct *s;
    // 将s1建立哈希表
    for (int i = 0; i < len; i++) {
        int a = s1[i];
        HASH_FIND_INT(users, &a, s);
        if (s) {
            s->count++;
        }
        else {
            s = (struct my_struct *)malloc(sizeof(struct my_struct));
            s->id = a;
            s->count = 1;
            HASH_ADD_INT(users, id, s);
        }
    }
    //循环每一个可能进行哈希匹配
    for (int i  = 0; s2[i + len - 1] != '\0'; i++) {
        if (isequal(s2 + i, len, users))
            return true;
    }
    return false;
}
```

26个字母的哈希匹配，我们完全可以用一个数组来表示。

```c
bool equals(int* cnt1, int* cnt2) {
    for (int i = 0; i < 26; i++) {
        if (cnt1[i] != cnt2[i]) {
            return false;
        }
    }
    return true;
}

bool checkInclusion(char* s1, char* s2) {
    int n = strlen(s1), m = strlen(s2);
    if (n > m) {
        return false;
    }
    int cnt1[26], cnt2[26];
    memset(cnt1, 0, sizeof(cnt1));
    memset(cnt2, 0, sizeof(cnt2));
    for (int i = 0; i < n; ++i) {
        ++cnt1[s1[i] - 'a'];
        ++cnt2[s2[i] - 'a'];
    }
    if (equals(cnt1, cnt2)) {
        return true;
    }
    for (int i = n; i < m; ++i) {
        ++cnt2[s2[i] - 'a'];
        --cnt2[s2[i - n] - 'a'];
        if (equals(cnt1, cnt2)) {
            return true;
        }
    }
    return false;
}
```

其实我们可以不用equal的，每次都是加进来一个和出去一个。我们可以记录两个数组的diff，进来一个缩小或者增大diff，出去的也可能缩小或者增大diff，直到diff == 0。代码就略去不表了。

我们还能用双指针来解这道题，具体看注释。

```c
bool checkInclusion(char* s1, char* s2) {
    int n = strlen(s1), m = strlen(s2);
    if (n > m) {
        return false;
    }
    int cnt[26];
    memset(cnt, 0, sizeof(cnt));
    for (int i = 0; i < n; ++i) {
        --cnt[s1[i] - 'a'];
    }
    int left = 0;
    for (int right = 0; right < m; ++right) {
        // right走过，说明入队，对应的个数++
        int x = s2[right] - 'a';
        ++cnt[x];
        // 如果大于0了，那么一定要出队，保持等于0，这里用while
        while (cnt[x] > 0) {
            --cnt[s2[left] - 'a'];
            ++left;
        }
        // 队列长度是m的时候，也就是找到答案的时候
        if (right - left + 1 == n) {
            return true;
        }
    }
    return false;
}
```

