### 6036. 构造字符串的总得分和

你需要从空字符串开始 **构造** 一个长度为 `n` 的字符串 `s` ，构造的过程为每次给当前字符串 **前面** 添加 **一个** 字符。构造过程中得到的所有字符串编号为 `1` 到 `n` ，其中长度为 `i` 的字符串编号为 `si` 。

- 比方说，`s = "abaca"` ，`s1 == "a"` ，`s2 == "ca"` ，`s3 == "aca"` 依次类推。

`si` 的 **得分** 为 `si` 和 `sn` 的 **最长公共前缀** 的长度（注意 `s == sn` ）。

给你最终的字符串 `s` ，请你返回每一个 `si` 的 **得分之和** 。

**示例 1：**

```
输入：s = "babab"
输出：9
解释：
s1 == "b" ，最长公共前缀是 "b" ，得分为 1 。
s2 == "ab" ，没有公共前缀，得分为 0 。
s3 == "bab" ，最长公共前缀为 "bab" ，得分为 3 。
s4 == "abab" ，没有公共前缀，得分为 0 。
s5 == "babab" ，最长公共前缀为 "babab" ，得分为 5 。
得分和为 1 + 0 + 3 + 0 + 5 = 9 ，所以我们返回 9 。
```

**示例 2 ：**

```
输入：s = "azbazbzaz"
输出：14
解释：
s2 == "az" ，最长公共前缀为 "az" ，得分为 2 。
s6 == "azbzaz" ，最长公共前缀为 "azb" ，得分为 3 。
s9 == "azbazbzaz" ，最长公共前缀为 "azbazbzaz" ，得分为 9 。
其他 si 得分均为 0 。
得分和为 2 + 3 + 9 = 14 ，所以我们返回 14 。
```

**提示：**

- `1 <= s.length <= 10^5`
- `s` 只包含小写英文字母。

#### Z 函数（拓展 KMP）

定义函数 z[i] 表示 s 和 s[i : n - 1] 的最长公共前缀。

```cpp
// C++ Version
vector<int> z_function(string s) {
  int n = (int)s.length();
  vector<int> z(n);
  // 维护 [l, r] 表示右端点最靠右的匹配段
  for (int i = 1, l = 0, r = 0; i < n; ++i) {
    // 根据定义，[l, r] 和 [0, r - l] 是相同的
    // 那么 i 就和 i - l 匹配，如果 z[i - l] 的匹配长度和小于 r - i + 1（即后面这一节的长度）
    // 说明后面无法匹配了，如果相等或者长了，那么必须一个一个继续试下去
    if (i <= r && z[i - l] < r - i + 1) {
      z[i] = z[i - l];
    } else {
      // 综合两种 else 的情况
      z[i] = max(0, r - i + 1);
      // 继续匹配
      while (i + z[i] < n && s[z[i]] == s[i + z[i]]) ++z[i];
    }
    // 更新 [l, r]
    if (i + z[i] - 1 > r) l = i, r = i + z[i] - 1;
  }
  return z;
}
```

那么这一题就可以这样写：

```cpp
class Solution {
public:
    long long sumScores(string s) {
        int n = s.size();
        vector<int> z(n, 0);
        long long ans = n;
        for (int i = 1, l = 0, r = 0; i < n; i++) {
            z[i] = max(min(z[i - l], r - i + 1), 0);
            while (i + z[i] < n && s[z[i]] == s[i + z[i]]) {
                z[i]++;
            }
            if (z[i] + i - 1 > r) l = i, r = z[i] + i - 1;
            ans += z[i];
        }
        return ans;
    }
};
```

#### 字符串哈希 + 二分

对于每个后缀 [i, n] 我们要判断它和前缀 [1, n - i + 1] 有多少相同的的。前面一直相同，到后面不再相同，满足二分的条件。

```cpp
typedef unsigned long long ull;
typedef long long ll;
const int N = 100010, P = 13331;
class Solution {
public:
    ull h[N], p[N];
    ull get(int l, int r)
    {
        return h[r] - h[l - 1] * p[r - l + 1];
    }
    ll sumScores(string s) {
        int n = s.length();
        p[0] = 1;
        for (int i = 1; i <= n; ++i) {
            h[i] = h[i - 1] * P + s[i - 1];
            p[i] = p[i - 1] * P;
        }
        ll res = 0;
        for (int i = n; i >= 1; --i) {
            // 
            int l = 0, r = n - i + 1;
            while (l < r) {
                // 这里 + 1，主要是因为后面 l = mid（防止无限循环）
                int mid = (l + r + 1) >> 1;
                if (get(1, mid) == get(i, i + mid - 1)) l = mid;
                else r = mid - 1;
            }
            res += l;
        }
        return res;
    }
};
```

这两种方式都可以来做 KMP 的题，下面做一下吧。

### 实现 strStr() 函数

> 给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串出现的第一个位置（下标从 0 开始）。如果不存在，则返回  -1 。
>
> 说明：
>
> - 当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。
>
> - 对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与 C 语言的 strstr() 以及 Java 的 indexOf() 定义相符。

示例 1：

```
输入：haystack = "hello", needle = "ll"
输出：2
```

示例 2：

```
输入：haystack = "aaaaa", needle = "bba"
输出：-1
```

示例 3：

```
输入：haystack = "", needle = ""
输出：0
```


提示：

- 1 <= haystack.length, needle.length <= 10^4
- haystack 和 needle 仅由小写英文字符组成

#### Z 函数

```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (needle == "")   return 0;
        string s = needle + "*" + haystack;
        int n = s.size();
        vector<int> z(n, 0);
        for (int i = 1, l = 0, r = 0; i < n; i++) {
            z[i] = max(min(z[i - l], r - i + 1), 0);
            while (i + z[i] < n && s[z[i]] == s[i + z[i]]) z[i]++;
            if (i + z[i] - 1 > r) l = i, r = i + z[i] - 1;
        }
        for (int i = 0; i < n; i++)
            if (z[i] == needle.size())
                return i - needle.size() - 1;
        return -1;
    }
};
```

#### 哈希

```cpp
const int N = 10010, P = 13331;
class Solution {
    unsigned long long hash[N], p[N];
    unsigned long long get(int i, int j)
    {
        return hash[j] - hash[i] * p[j - i];
    }
public:
    int strStr(string haystack, string needle) {
        hash[0] = 0, p[0] = 1;
        for (int i = 1; i <= haystack.size(); i++) {
            hash[i] = hash[i - 1] * P + haystack[i - 1];
            p[i] = p[i - 1] * P;
        }
        unsigned long long key = 0;
        for (int i = 1; i <= needle.size(); i++) 
            key = key * P + needle[i - 1];
        int n = needle.size();
        if (n == 0) return 0;
        for (int i = 0; i < haystack.size(); i++) {
            int j = i + n;
            if (j > haystack.size()) continue;
            if (get(i, j) == key)
                return i;
        }
        return -1;
    }
};
```

