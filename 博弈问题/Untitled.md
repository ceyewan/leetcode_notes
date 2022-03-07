### 石子游戏

> Alice 和 Bob 用几堆石子在做游戏。一共有偶数堆石子，排成一行；每堆都有正整数颗石子，数目为 piles[i] 。
>
> 游戏以谁手中的石子最多来决出胜负。石子的 总数是奇数 ，所以没有平局。
>
> Alice 和 Bob 轮流进行，Alice 先开始 。 每回合，玩家从行的开始或结束处取走整堆石头。 这种情况一直持续到没有更多的石子堆为止，此时手中石子最多的玩家 获胜 。
>
> 假设 Alice 和 Bob 都发挥出最佳水平，当 Alice 赢得比赛时返回 true ，当 Bob 赢得比赛时返回 false 。

示例 1：

```
输入：piles = [5,3,4,5]
输出：true
解释：
Alice 先开始，只能拿前 5 颗或后 5 颗石子 。
假设他取了前 5 颗，这一行就变成了 [3,4,5] 。
如果 Bob 拿走前 3 颗，那么剩下的是 [4,5]，Alice 拿走后 5 颗赢得 10 分。
如果 Bob 拿走后 5 颗，那么剩下的是 [3,4]，Alice 拿走后 4 颗赢得 9 分。
这表明，取前 5 颗石子对 Alice 来说是一个胜利的举动，所以返回 true 。
```

示例 2：

```
输入：piles = [3,7,2,3]
输出：true
```


提示：

- 2 <= piles.length <= 500
- piles.length 是 偶数
- 1 <= piles[i] <= 500
- sum(piles[i]) 是 奇数

记忆化搜索，这里我搞了五个状态，剩下的石子的起始下标和结束下标，两人的得分，以及谁先手。

```python
class Solution:
    def stoneGame(self, piles: List[int]) -> bool:
        @cache
        # 表示考虑石子从 start 到 end ，alice 有 count1 的石子，bob 有 count2 的石子
        # alice 是否先手的前提下，alice 能否获胜
        def search(start: int, end: int, count1: int, count2: int, sign: bool):
            # 先手并且只有一个了，就拿走
            if sign and start == end:
                return count1 + piles[start] > count2
            # 对方先手，就被对方拿走
            if start == end:
                return count1 > count2 + piles[start]
            if sign:
                # 先手，可以拿前面或者后面的
                return search(start + 1, end, count1 + piles[start], count2, False) or search(start, end - 1, count1 + piles[end], count2, False)
            else:
                # 后手，对方也可以拿前面的或者后面的，并且只有两种可能都是 true 它才会给你 true
                return search(start + 1, end, count1, count2 + piles[start], True) and search(start, end - 1,count1, count2 + piles[end], True)
        return search(0, len(piles) - 1, 0, 0, True)
```

其实我们也可以把两个人的得分之差作为函数的返回值。

```python
class Solution:
    def stoneGame(self, piles: List[int]) -> bool:
        @cache
        def search(start: int, end: int, sign: bool):
            if sign and start == end:
                return piles[start]
            if start == end:
                return -piles[start]
            if sign:
                # 两种可以性取最大值（alice 回合，自然要让得分变大
                return max(piles[start] + search(start + 1, end,  False), piles[end] + search(start, end - 1, False))
            else:
                # 两种可以性取最小值（bob 的回合，自然要让得分小
                return min(search(start + 1, end,  True) - piles[start], search(start, end - 1, True) - piles[end])
        return search(0, len(piles) - 1, True) > 0
```

我们可以看到，piles 的长度是偶数，那么就是说剩余长度是偶数 alice 先手，剩余长度是奇数 bob 先手。由此，继续省略表示先手的那个状态。

```python
class Solution:
    def stoneGame(self, piles: List[int]) -> bool:
        @cache
        def search(start: int, end: int):
            if start == end: # 长度为奇数
                return -piles[start]
            if (end - start + 1) % 2 == 0: # 长度为偶数
                return max(piles[start] + search(start + 1, end), piles[end] + search(start, end - 1))
            else:
                return min(search(start + 1, end) - piles[start], search(start, end - 1) - piles[end])
        return search(0, len(piles) - 1) > 0
```

然后我们可以把记忆化搜索优化成动态规划。二维动态规划又可以优化为一维的，不过大可不必了。

数学方法，我们把奇数分为一组，偶数分为一组。一定有一组数量要多，alice 可以一直取奇数组或者偶数组，那么必赢。

```python
class Solution:
    def stoneGame(self, piles: List[int]) -> bool:
        return True
```

### 石子游戏Ⅱ

> 爱丽丝和鲍勃继续他们的石子游戏。许多堆石子 排成一行，每堆都有正整数颗石子 piles[i]。游戏以谁手中的石子最多来决出胜负。
>
> 爱丽丝和鲍勃轮流进行，爱丽丝先开始。最初，M = 1。
>
> 在每个玩家的回合中，该玩家可以拿走剩下的 前 X 堆的所有石子，其中 1 <= X <= 2M。然后，令 M = max(M, X)。
>
> 游戏一直持续到所有石子都被拿走。
>
> 假设爱丽丝和鲍勃都发挥出最佳水平，返回爱丽丝可以得到的最大数量的石头。

示例 1：

```
输入：piles = [2,7,9,4,4]
输出：10
解释：如果一开始Alice取了一堆，Bob取了两堆，然后Alice再取两堆。爱丽丝可以得到2 + 4 + 4 = 10堆。如果Alice一开始拿走了两堆，那么Bob可以拿走剩下的三堆。在这种情况下，Alice得到2 + 7 = 9堆。返回10，因为它更大。
```

示例 2:

```
输入：piles = [1,2,3,4,5,100]
输出：104
```


提示：

- 1 <= piles.length <= 100
- 1 <= piles[i] <= 10^4

```python
class Solution:
    def stoneGameII(self, piles: List[int]) -> int:
        @cache
        def search(start: int, m: int, sign: bool):
            # 可以一口气搞完了，还留个屁给对手
            if start + 2 * m >= len(piles):
                ans = 0
                for i in range(start, len(piles)):
                    ans += piles[i]
                # alice 先手
                if sign:    return ans
                # alice 后手（还剩个屁）
                else:       return 0
            # alice 先手
            if sign:
                ans = now = 0
                for i in range(start, start + 2 * m):
                    # now 表示这一轮的得分
                    now += piles[i]
                    # 取 max
                    ans = max(ans, now + search(i + 1, max(m, i - start + 1), False))
                return ans
            else:
                ans = 1e9
                for i in range(start, start + 2 * m):
                    # 对方的回合，自然要让你得分尽可能小
                    ans = min(ans, search(i + 1, max(m, i - start + 1), True))
                return ans
        return search(0, 1, True)
```

### 石子游戏Ⅲ

> Alice 和 Bob 用几堆石子在做游戏。几堆石子排成一行，每堆石子都对应一个得分，由数组 stoneValue 给出。
>
> Alice 和 Bob 轮流取石子，Alice 总是先开始。在每个玩家的回合中，该玩家可以拿走剩下石子中的的前 1、2 或 3 堆石子 。比赛一直持续到所有石头都被拿走。
>
> 每个玩家的最终得分为他所拿到的每堆石子的对应得分之和。每个玩家的初始分数都是 0 。比赛的目标是决出最高分，得分最高的选手将会赢得比赛，比赛也可能会出现平局。
>
> 假设 Alice 和 Bob 都采取 最优策略 。如果 Alice 赢了就返回 "Alice" ，Bob 赢了就返回 "Bob"，平局（分数相同）返回 "Tie" 。

示例 1：

```
输入：values = [1,2,3,7]
输出："Bob"
解释：Alice 总是会输，她的最佳选择是拿走前三堆，得分变成 6 。但是 Bob 的得分为 7，Bob 获胜。
```

示例 2：

```
输入：values = [1,2,3,-9]
输出："Alice"
解释：Alice 要想获胜就必须在第一个回合拿走前三堆石子，给 Bob 留下负分。
如果 Alice 只拿走第一堆，那么她的得分为 1，接下来 Bob 拿走第二、三堆，得分为 5 。之后 Alice 只能拿到分数 -9 的石子堆，输掉比赛。
如果 Alice 拿走前两堆，那么她的得分为 3，接下来 Bob 拿走第三堆，得分为 3 。之后 Alice 只能拿到分数 -9 的石子堆，同样会输掉比赛。
注意，他们都应该采取 最优策略 ，所以在这里 Alice 将选择能够使她获胜的方案。
```

示例 3：

```
输入：values = [1,2,3,6]
输出："Tie"
解释：Alice 无法赢得比赛。如果她决定选择前三堆，她可以以平局结束比赛，否则她就会输。
```

示例 4：

```
输入：values = [1,2,3,-1,-2,-3,7]
输出："Alice"
```

示例 5：

```
输入：values = [-1,-2,-3]
输出："Tie"
```


提示：

- 1 <= values.length <= 50000
- -1000 <= values[i] <= 1000

因为有正有负，所以不能说还剩下 3 个以内就全拿走。

```python
class Solution:
    def stoneGameIII(self, stoneValue: List[int]) -> str:
        length = len(stoneValue)
        @cache
        # 表示从 start 开始，是不是 Alice 的回合两个之间的得分差
        def search(start: int, sign: bool):
            if sign:
                ans = -1e9
                now = 0
                for i in range(start, min(start + 3, length)):
                    # 当前 alice 拿的
                    now += stoneValue[i]
                    if i + 1 == length:
                        ans = max(ans, now)
                    else:
                        ans = max(ans, now + search(i + 1, False))
                return ans
            else:
                ans = 1e9
                now = 0
                for i in range(start, min(start + 3, length)):
                    # 当前 bob 拿的
                    now += stoneValue[i]
                    if i + 1 == length:
                        ans = min(ans, -now)
                    else:
                        ans = min(ans, search(i + 1, True) - now)
                return ans
        a = search(0, True)
        if a > 0:       return "Alice"
        elif a == 0:    return "Tie"
        else:           return "Bob"
```

### 石子游戏Ⅳ

> Alice 和 Bob 两个人轮流玩一个游戏，Alice 先手。
>
> 一开始，有 n 个石子堆在一起。每个人轮流操作，正在操作的玩家可以从石子堆里拿走任意非零平方数个石子。
>
> 如果石子堆里没有石子了，则无法操作的玩家输掉游戏。
>
> 给你正整数 n ，且已知两个人都采取最优策略。如果 Alice 会赢得比赛，那么返回 True ，否则返回 False 。

示例 1：

```
输入：n = 1
输出：true
解释：Alice 拿走 1 个石子并赢得胜利，因为 Bob 无法进行任何操作。
```

示例 2：

```
输入：n = 2
输出：false
解释：Alice 只能拿走 1 个石子，然后 Bob 拿走最后一个石子并赢得胜利（2 -> 1 -> 0）。
```

示例 3：

```
输入：n = 4
输出：true
解释：n 已经是一个平方数，Alice 可以一次全拿掉 4 个石子并赢得胜利（4 -> 0）。
```

示例 4：

```
输入：n = 7
输出：false
解释：当 Bob 采取最优策略时，Alice 无法赢得比赛。
如果 Alice 一开始拿走 4 个石子， Bob 会拿走 1 个石子，然后 Alice 只能拿走 1 个石子，Bob 拿走最后一个石子并赢得胜利（7 -> 3 -> 2 -> 1 -> 0）。
如果 Alice 一开始拿走 1 个石子， Bob 会拿走 4 个石子，然后 Alice 只能拿走 1 个石子，Bob 拿走最后一个石子并赢得胜利（7 -> 6 -> 2 -> 1 -> 0）。
```

示例 5：

```
输入：n = 17
输出：false
解释：如果 Bob 采取最优策略，Alice 无法赢得胜利。
```


提示：

- 1 <= n <= 10^5

```python
class Solution:
    def winnerSquareGame(self, n: int) -> bool:
        @cache
        # n 的大小和当前的回合，返回 alice 是否胜利
        def search(num: int, sign: bool):
            if num == 0 and sign:   return False
            if num == 0:            return True
            if sign:
                i = 1
                ans = False
                while i * i <= num:
                    # 只要有一种赢得可能性就行
                    ans = ans or search(num - i * i, False)
                    if ans: return True
                    i += 1
                return ans
            else:
                i = 1
                ans = True
                while i * i <= num:
                    # 全部都赢才行。这是对手的回合，如果有一种可能不行，那么对手当然就会选择那种
                    ans = ans and search(num - i * i, True)
                    if not ans: return False
                    i += 1
                return ans
        return search(n, True)
```

### 石子游戏Ⅴ

> 几块石子 排成一行 ，每块石子都有一个关联值，关联值为整数，由数组 stoneValue 给出。
>
> 游戏中的每一轮：Alice 会将这行石子分成两个 非空行（即，左侧行和右侧行）；Bob 负责计算每一行的值，即此行中所有石子的值的总和。Bob 会丢弃值最大的行，Alice 的得分为剩下那行的值（每轮累加）。如果两行的值相等，Bob 让 Alice 决定丢弃哪一行。下一轮从剩下的那一行开始。
>
> 只 剩下一块石子 时，游戏结束。Alice 的分数最初为 0 。
>
> 返回 Alice 能够获得的最大分数 。

示例 1：

```
输入：stoneValue = [6,2,3,4,5,5]
输出：18
解释：在第一轮中，Alice 将行划分为 [6，2，3]，[4，5，5] 。左行的值是 11 ，右行的值是 14 。Bob 丢弃了右行，Alice 的分数现在是 11 。
在第二轮中，Alice 将行分成 [6]，[2，3] 。这一次 Bob 扔掉了左行，Alice 的分数变成了 16（11 + 5）。
最后一轮 Alice 只能将行分成 [2]，[3] 。Bob 扔掉右行，Alice 的分数现在是 18（16 + 2）。游戏结束，因为这行只剩下一块石头了。
```

示例 2：

```
输入：stoneValue = [7,7,7,7,7,7,7]
输出：28
```

示例 3：

```
输入：stoneValue = [4]
输出：0
```


提示：

- 1 <= stoneValue.length <= 500
- 1 <= stoneValue[i] <= 10^6

这里的切片和我们可以提前求好待用，这样同样可以减少重复计算， 但是不能再用 cache 了，cache 只能一个函数用。

```python
class Solution:
    def stoneGameV(self, stoneValue: List[int]) -> int:
        @cache
        def search(start: int, end: int):
            if start == end:
                return 0
            if start == end - 1:
                return min(stoneValue[start], stoneValue[end])
            ans = 0
            total = sum(stoneValue[start:end+1])
            a = 0
            for i in range(start, end):
                # 滑动窗口求前切片和
                a += stoneValue[i]
                # 取差求后切片和
                b = total - a
 				# 取小的那一片段
                if a < b:
                    ans = max(ans, a + search(start, i))
                # 随便 alice 选择，自然选择大的
                elif a == b:
                    ans = max(ans, a + search(start, i))
                    ans = max(ans, b + search(i + 1, end))
                # 选小的
                else:
                    ans = max(ans, b + search(i + 1, end))
            return ans
        return search(0, len(stoneValue) - 1)

```

### 石子游戏Ⅵ

> Alice 和 Bob 轮流玩一个游戏，Alice 先手。
>
> 一堆石子里总共有 n 个石子，轮到某个玩家时，他可以 移出 一个石子并得到这个石子的价值。Alice 和 Bob 对石子价值有 不一样的的评判标准 。双方都知道对方的评判标准。
>
> 给你两个长度为 n 的整数数组 `aliceValues` 和 `bobValues` 。`aliceValues[i]` 和 `bobValues[i]` 分别表示 `Alice` 和 `Bob` 认为第 i 个石子的价值。
>
> 所有石子都被取完后，得分较高的人为胜者。如果两个玩家得分相同，那么为平局。两位玩家都会采用 最优策略 进行游戏。
>
> 请你推断游戏的结果，用如下的方式表示：
>
> - 如果 Alice 赢，返回 1 。
> - 如果 Bob 赢，返回 -1 。
> - 如果游戏平局，返回 0 。


示例 1：

```
输入：aliceValues = [1,3], bobValues = [2,1]
输出：1
解释：
如果 Alice 拿石子 1 （下标从 0开始），那么 Alice 可以得到 3 分。
Bob 只能选择石子 0 ，得到 2 分。
Alice 获胜。
```

示例 2：

```
输入：aliceValues = [1,2], bobValues = [3,1]
输出：0
解释：
Alice 拿石子 0 ， Bob 拿石子 1 ，他们得分都为 1 分。
打平。
```

示例 3：

```
输入：aliceValues = [2,4,3], bobValues = [1,6,7]
输出：-1
解释：
不管 Alice 怎么操作，Bob 都可以得到比 Alice 更高的得分。
比方说，Alice 拿石子 1 ，Bob 拿石子 2 ， Alice 拿石子 0 ，Alice 会得到 6 分而 Bob 得分为 7 分。
Bob 会获胜。
```


提示：

- n == aliceValues.length == bobValues.length
- 1 <= n <= 10^5
- 1 <= aliceValues[i], bobValues[i] <= 100

这里不能再动态规划了，没有状态转移，因为石子是随便选的。一个石子不仅能让 alice 获得 `aliceValues[i]` 还能使 bob 减少 `bobValues[i]`，因此这枚石子的价值相当于 `value[i] = aliceValues[i] + bobValues[i]`。那么 alice 就贪心的选择价值高的呗， bob 也选择价值高的。

alice 就会是 1 3 5 7 9，bob 是 2 4 6 8 

并且alice 的得分是 value - bob[i]，然后再减去 bob 的得分，和起来就是 `sum(bobValues)`。

```python
class Solution:
    def stoneGameVI(self, aliceValues: List[int], bobValues: List[int]) -> int:
        value = [0] * len(aliceValues)
        for i in range(len(aliceValues)):
            value[i] = aliceValues[i] + bobValues[i]
        value.sort(reverse=True)
        ans = sum(value[::2]) - sum(bobValues)
        if ans > 0: return 1
        elif ans == 0: return 0
        else: return -1
```

### 石子游戏Ⅶ

> 石子游戏中，爱丽丝和鲍勃轮流进行自己的回合，爱丽丝先开始 。
>
> 有 n 块石子排成一排。每个玩家的回合中，可以从行中 移除 最左边的石头或最右边的石头，并获得与该行中剩余石头值之 和 相等的得分。当没有石头可移除时，得分较高者获胜。
>
> 鲍勃发现他总是输掉游戏（可怜的鲍勃，他总是输），所以他决定尽力 减小得分的差值 。爱丽丝的目标是最大限度地 扩大得分的差值 。
>
> 给你一个整数数组 stones ，其中 stones[i] 表示 从左边开始 的第 i 个石头的值，如果爱丽丝和鲍勃都 发挥出最佳水平 ，请返回他们 得分的差值 。

示例 1：

```
输入：stones = [5,3,1,4,2]
输出：6
解释：

- 爱丽丝移除 2 ，得分 5 + 3 + 1 + 4 = 13 。游戏情况：爱丽丝 = 13 ，鲍勃 = 0 ，石子 = [5,3,1,4] 。
- 鲍勃移除 5 ，得分 3 + 1 + 4 = 8 。游戏情况：爱丽丝 = 13 ，鲍勃 = 8 ，石子 = [3,1,4] 。
- 爱丽丝移除 3 ，得分 1 + 4 = 5 。游戏情况：爱丽丝 = 18 ，鲍勃 = 8 ，石子 = [1,4] 。
- 鲍勃移除 1 ，得分 4 。游戏情况：爱丽丝 = 18 ，鲍勃 = 12 ，石子 = [4] 。
- 爱丽丝移除 4 ，得分 0 。游戏情况：爱丽丝 = 18 ，鲍勃 = 12 ，石子 = [] 。
  得分的差值 18 - 12 = 6 。
```

示例 2：

```
输入：stones = [7,90,5,1,100,10,10,2]
输出：122
```


提示：

- n == stones.length
- 2 <= n <= 1000
- 1 <= stones[i] <= 1000

```python
class Solution:
    def stoneGameVII(self, stones: List[int]) -> int:
        # dp[i][j] 表示下标 i 到下标 j 的区间和
        dp = [[0] * len(stones) for _ in range(len(stones))]
        for i in range(len(stones)):
            for j in range(i, len(stones)):
                if i == j:
                    dp[i][j] = stones[j]
                dp[i][j] = dp[i][j - 1] + stones[j]
        @cache
        def search(start: int, end: int, sign: bool):
            if start == end:
                return 0
            if sign: # alice 回合，要让得分尽可能大
                ans = -1e9
                # 拿掉最后一个，得分就是 dp[start][end - 1]
                ans = max(ans, dp[start][end - 1] + search(start, end - 1, False))
                # 拿掉第一个，得分就是 dp[start + 1][end]
                ans = max(ans, dp[start + 1][end] + search(start + 1, end, False))
                return ans
            else: # bob 回合，要让得分尽可能小
                ans = 1e9
                ans = min(ans, - dp[start][end - 1] + search(start, end - 1, True))
                ans = min(ans, - dp[start + 1][end] + search(start + 1, end, True))
                return ans
        return search(0, len(stones) - 1, True)
```

### 石子游戏Ⅷ

> Alice 和 Bob 玩一个游戏，两人轮流操作， Alice 先手 。
>
> 总共有 n 个石子排成一行。轮到某个玩家的回合时，如果石子的数目 大于 1 ，他将执行以下操作：
>
> 选择一个整数 x > 1 ，并且 移除 最左边的 x 个石子。
> 将 移除 的石子价值之 和 累加到该玩家的分数中。
> 将一个 新的石子 放在最左边，且新石子的值为被移除石子值之和。
> 当只剩下 一个 石子时，游戏结束。
>
> Alice 和 Bob 的 分数之差 为 (Alice 的分数 - Bob 的分数) 。 Alice 的目标是 最大化 分数差，Bob 的目标是 最小化 分数差。
>
> 给你一个长度为 n 的整数数组 stones ，其中 stones[i] 是 从左边起 第 i 个石子的价值。请你返回在双方都采用 最优 策略的情况下，Alice 和 Bob 的 分数之差 。

示例 1：

```
输入：stones = [-1,2,-3,4,-5]
输出：5
解释：

- Alice 移除最左边的 4 个石子，得分增加 (-1) + 2 + (-3) + 4 = 2 ，并且将一个价值为 2 的石子放在最左边。stones = [2,-5] 。
- Bob 移除最左边的 2 个石子，得分增加 2 + (-5) = -3 ，并且将一个价值为 -3 的石子放在最左边。stones = [-3] 。
  两者分数之差为 2 - (-3) = 5 。
```

示例 2：

```
输入：stones = [7,-6,5,10,5,-2,-6]
输出：13
解释：

- Alice 移除所有石子，得分增加 7 + (-6) + 5 + 10 + 5 + (-2) + (-6) = 13 ，并且将一个价值为 13 的石子放在最左边。stones = [13] 。
  两者分数之差为 13 - 0 = 13 。
```

示例 3：

```
输入：stones = [-10,-12]
输出：-22
解释：

- Alice 只有一种操作，就是移除所有石子。得分增加 (-10) + (-12) = -22 ，并且将一个价值为 -22 的石子放在最左边。stones = [-22] 。
  两者分数之差为 (-22) - 0 = -22 。
```


提示：

- n == stones.length
- 2 <= n <= 10^5
- -10^4 <= stones[i] <= 10^4

```python
class Solution:
    def stoneGameVIII(self, stones: List[int]) -> int:
        # dp[i] 表示到下标 i 的前缀和
        dp = [0] * len(stones)
        dp[0] = stones[0]
        for i in range(len(dp)):
            dp[i] = dp[i - 1] + stones[i]
        @cache
        def search(start: int, sign: bool):
            # 全捡走呗
            if start == len(stones) - 1 and sign:
                return dp[start]
           	# 全是对手的
            if start == len(stones) - 1 and not sign:
                return -dp[start]
            if sign: # 尽可能扩大得分
                # 不取到 start 截止，那么就 search(start + 1, True) 
                # 从 start + 1 开始考虑，并且还是先手
                # 取到 start 截止，得分为 dp[start]
                # 从 start + 1 开始考虑，并且y
                return max(search(start + 1, True), dp[start] + search(start + 1, False))
            else: # 尽可能缩小得分差
                return min(search(start + 1, False), -dp[start] + search(start + 1, True))
        # 第一次必须从下标 1 开始（后面只要下标比上次大即可
        return search(1, True)
```

### 石子游戏Ⅸ

> Alice 和 Bob 再次设计了一款新的石子游戏。现有一行 n 个石子，每个石子都有一个关联的数字表示它的价值。给你一个整数数组 stones ，其中 stones[i] 是第 i 个石子的价值。
>
> Alice 和 Bob 轮流进行自己的回合，Alice 先手。每一回合，玩家需要从 stones 中移除任一石子。
>
> 如果玩家移除石子后，导致 所有已移除石子 的价值 总和 可以被 3 整除，那么该玩家就 输掉游戏 。
> 如果不满足上一条，且移除后没有任何剩余的石子，那么 Bob 将会直接获胜（即便是在 Alice 的回合）。
> 假设两位玩家均采用 最佳 决策。如果 Alice 获胜，返回 true ；如果 Bob 获胜，返回 false 。

示例 1：

```
输入：stones = [2,1]
输出：true
解释：游戏进行如下：

- 回合 1：Alice 可以移除任意一个石子。
- 回合 2：Bob 移除剩下的石子。 
  已移除的石子的值总和为 1 + 2 = 3 且可以被 3 整除。因此，Bob 输，Alice 获胜。
```

示例 2：

```
输入：stones = [2]
输出：false
解释：Alice 会移除唯一一个石子，已移除石子的值总和为 2 。 
由于所有石子都已移除，且值总和无法被 3 整除，Bob 获胜。
```

示例 3：

```
输入：stones = [5,1,2,4,3]
输出：false
解释：Bob 总会获胜。其中一种可能的游戏进行方式如下：

- 回合 1：Alice 可以移除值为 1 的第 2 个石子。已移除石子值总和为 1 。
- 回合 2：Bob 可以移除值为 3 的第 5 个石子。已移除石子值总和为 = 1 + 3 = 4 。
- 回合 3：Alices 可以移除值为 4 的第 4 个石子。已移除石子值总和为 = 1 + 3 + 4 = 8 。
- 回合 4：Bob 可以移除值为 2 的第 3 个石子。已移除石子值总和为 = 1 + 3 + 4 + 2 = 10.
- 回合 5：Alice 可以移除值为 5 的第 1 个石子。已移除石子值总和为 = 1 + 3 + 4 + 2 + 5 = 15.
  Alice 输掉游戏，因为已移除石子值总和（15）可以被 3 整除，Bob 获胜。
```


提示：

- 1 <= stones.length <= 10^5
- 1 <= stones[i] <= 10^4

我们先求出模 3 余 0、1、2 的个数，然后记忆化搜索。

- 余数为 0 的个数为偶数，可以相互抵消掉，没有影响。
- 余数为 0 的个数为奇数，会有影响。比如 [3] 就可以让 alice 输；[2, 3] 有这个 3 但是 alice 还是输，不能认为是反转输赢。

记忆化搜索，状态有五个，余数是 0、1、2 的个数，当前和的余数，和当前的回合。

```python
class Solution:
    def stoneGameIX(self, stones: List[int]) -> bool:
        # 计算不同余数的个数
        a = [0, 0, 0]
        for num in stones:
            a[num % 3] += 1
        @cache
        def search(zero: int, one: int, two: int, num: int, sign: bool):
            # 如果都用完了， alice 输
            if zero == 0 and one == 0 and two == 0:
                return False
            # alice 的回合，当前余数是 2 ，我们不能选 1
            if num == 2 and sign:
                if zero == 0 and two == 0:
                    return False
                elif zero == 0:
                    return search(zero, one, two - 1, 1, False)
                elif two == 0:
                    return search(zero - 1, one, two, 2, False)
                else:
                    return search(zero - 1, one, two, 2, False) or search(zero, one, two - 1, 1, False)
            # 对手的回合
            if num == 2 and not sign:
                if zero == 0 and two == 0:
                    return True
                elif zero == 0:
                    return search(zero, one, two - 1, 1, True)
                elif two == 0:
                    return search(zero - 1, one, two, 2, True)
                else:
                    return search(zero - 1, one, two, 2, True) or search(zero, one, two - 1, 1, True)
			# alice 的回合，当前余数是 1 ，我们不能选 2
            if num == 1 and sign:
                if zero == 0 and one == 0:
                    return False
                elif zero == 0:
                    return search(zero, one - 1, two, 2, False)
                elif one == 0:
                    return search(zero - 1, one, two, 1, False)
                else:
                    return search(zero, one - 1, two, 2, False) or search(zero - 1, one, two, 1, False)
            # 对手的回合
            if num == 1 and not sign:
                if zero == 0 and one == 0:
                    return True
                elif zero == 0:
                    return search(zero, one - 1, two, 2, True)
                elif one == 0:
                    return search(zero - 1, one, two, 1, True)
                else:
                    return search(zero, one - 1, two, 2, True) or search(zero - 1, one, two, 1, True)
        # 偶数个可以互相抵消
        a[0] = a[0] % 2
        # 第一步可以选择余数为 1 或者 2 的
        return (a[1] > 0 and search(a[0], a[1] - 1, a[2], 1, False)) or (a[2] > 0 and search(a[0], a[1], a[2] - 1, 2, False))
```

我们可以发现，第一个取 2 ，后面就得取 2，然后取 1，然后取 2。即 2212121212121… 或者 11212121212…

```python
class Solution:
    def stoneGameIX(self, stones: List[int]) -> bool:
        cnt0 = cnt1 = cnt2 = 0
        for val in stones:
            if (typ := val % 3) == 0:
                cnt0 += 1
            elif typ == 1:
                cnt1 += 1
            else:
                cnt2 += 1
        # 如果偶数个余数为0
        if cnt0 % 2 == 0:
            # alice 可以决定自己一直是 1 还是 2，他会优先选择数量多的
            # 但是需要至少一个相反的来启动
            # 比如 1 启动，后面就确实 2。2 启动后面就全是 1
            return cnt1 >= 1 and cnt2 >= 1
        # 如果类型 0 的石子的个数为奇数，那么 Alice 获胜当且仅当
        #「在没有类型 0 石子的情况下，Bob 获胜且原因不是因为所有石子都被移除」。
        # 对应到上面的分析即为「类型 1 的石子比类型 2 多超过 2 个」或者
        #「类型 2 的石子比类型 1 多超过 2 个」。
        # 要得是循环不下去了，而不是xu'na
        return cnt1 - cnt2 > 2 or cnt2 - cnt1 > 2
```

