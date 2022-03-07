### 79、单词搜索

>给定一个 m x n 二维字符网格 board 和一个字符串单词 word 。如果 word 存在于网格中，返回 true ；否则，返回 false 。
>
>单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。
>
>提示：
>
>- m == board.length
>- n = board[i].length
>- 1 <= m, n <= 6
>- 1 <= word.length <= 15
>- board 和 word 仅由大小写英文字母组成

题意就是矩阵里面有没有哪个弯弯折折一条线就是 word 这个单词的。

### 思路

最开始是想要用 bfs 搜索的，如果字母满足和 word 中对应的字母相等就入队，但是失败了。问题出在，假如我下一个选择可以是 E1 和 E2 ，我先试了 E1 ，发现失败了（这个时候我用了 E1 ）， E2 成功但是需要用到 E1 ，可是我之前标记过这个 E1 了，那么我就得不到正确结果了。没办法取消标记，因为 E1 和 E2 在 bfs 中没什么先后顺序。

回溯

```c
int sign[6][6];// 标记是否使用过了
int dir[4][2] = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
bool huisu(char **board, int m, int n, int i, int j, int count, int length, char *word)
{
    if (count == length)    return true;
    int judge = false;
    for (int k = 0; k < 4; k++) {
        int x = i + dir[k][0];
        int y = j + dir[k][1];
        if (0 <= x && x < m && 0 <= y && y < n && board[x][y] == word[count] && !sign[x][y]) {
            sign[x][y] = 1;
            judge = huisu(board, m, n, x, y, count + 1, length, word);
            sign[x][y] = 0;
            if (judge)  return true;
        }
    }
    return false;
}
bool exist(char** board, int boardSize, int* boardColSize, char * word){
    memset(sign, 0, sizeof(sign));
    bool judge = false;
    int length = strlen(word);
    if (length > boardSize * boardColSize[0])   return false;
    for (int i = 0; i < boardSize; i++) {
        for (int j = 0; j < boardColSize[0]; j++) {
            if (board[i][j] == word[0]) {
                sign[i][j] = 1;
                judge = huisu(board, boardSize, boardColSize[0], i, j, 1, length, word);
                sign[i][j] = 0;
            }
            if (judge)  return true;
        }
    }
    return false;
}
```

### 22、括号生成

```c
char **generateParenthesis(int n, int *returnSize)
{
    *returnSize = 0;
    if (n == 0)
        return NULL;
    if (n == 1) {
        char *temp = (char *)malloc(sizeof(char) * 3);
        temp[0] = '(';
        temp[1] = ')';
        temp[2] = '\0';
        char **result = (char **)malloc(sizeof(char *));
        result[(*returnSize)++] = temp;
        return result;
    }
    char **result = (char **)malloc(sizeof(char *) * pow(2, 2 * n));
    int returnSize1, returnSize2;
    char **result1;
    char **result2;
    for (int i = 0; i < n; i++) {
        result1 = generateParenthesis(i, &returnSize1);
        result2 = generateParenthesis(n - 1- i, &returnSize2);
        for (int j = 0; j <= returnSize1; j++) {
            for (int k = 0; k <= returnSize2; k++) {
                char *temp = (char *)malloc(sizeof(char) * (2 * n + 1));
                if ((j > 0 && j == returnSize1) || k > 0 && (k == returnSize2))
                    continue;
                temp[0] = '(';
                int n = 1, m = 0;
                while (j < returnSize1 && result1[j][m] != '\0') {
                    temp[n++] = result1[j][m++];
                }
                temp[n++] = ')';
                m = 0;
                while (k < returnSize2 && result2[k][m] != '\0') {
                    temp[n++] = result2[k][m++];
                }
                temp[n] = '\0';
                result[(*returnSize)++] = temp;
            }
        }
    }
    return result;
}
```

### 17、电话号码的字母组合

```c
char phone_map[10][5] = {"\0", "\0", "abc\0", "def\0", "ghi\0", "jkl\0", "mno\0", "pqrs\0", "tuv\0", "wxyz\0"};

char **results;
int results_size;

char *result;
int result_size;

int digits_size;

void huishu(char *digits, int index)//回溯的层数
{
    if (index == digits_size) {
        char *temp = (char *)malloc(sizeof(char) * (result_size + 1));
        memcpy(temp, result, sizeof(char) * (result_size + 1));
        results[results_size++] = temp; 
    }
    else {
        char digit = digits[index]; //
        char *letter = phone_map[digit - '0']; //
        for (int i = 0; i < strlen(letter); i++) {
            result[result_size++] = letter[i];
            result[result_size] = '\0';
            huishu(digits, index + 1);
            result_size--;
        }
    }
}
char **letterCombinations(char *digits, int *returnSize)
{
    result_size = results_size = 0;
    digits_size = strlen(digits); //电话号码的长度
    if (digits_size == 0) {
        *returnSize = 0;
        return results;
    }
    int num = pow(4, digits_size);
              // 每个数字最多对应4个字母，那么就是说最多有4的digits_size次方个答案
    results = (char **)malloc(sizeof(char *) * num);
    result = (char *)malloc(sizeof(char *) * (digits_size + 1));
    huishu(digits, 0);
    *returnSize = results_size;
    return results;
}
```

