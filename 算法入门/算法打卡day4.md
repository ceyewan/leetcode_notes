### 344、反转字符串

左指针和右指针相互交换即可。

```c
void reverseString(char* s, int sSize){
    int left = 0, right = sSize - 1;
    while (left < right) {
        char temp = s[left];
        s[left] = s[right];
        s[right] = temp;
        left++;
        right--;
    }
}
```

### 557、反转字符串中的单词Ⅲ

左右指针分别指向单词的首尾即可，然后用temp指向单词的后一次位置，是空格就重复，是'\0'就结束。

```c

char * reverseWords(char * s){
    int left = 0, right = 0;
    int temp = 0;
    while (s[temp] != '\0') {
        while (s[right + 1] != ' ' && s[right + 1] != '\0')
            right++;
        temp = right + 1;
        while (left < right) {
            int tmp = s[left];
            s[left] = s[right];
            s[right] = tmp;
            left++;
            right--;
        }
        if (s[temp] != '\0') {
            left = temp + 1;
            right = temp + 1;
        }
    }
    return s;
}
```

