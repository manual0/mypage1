+++
title = "动态规划"
draft = false
date = "2025-05-09"
tags = ["学习","算法"]
+++

首先，虽然动态规划的核心思想就是穷举求最值，但是问题可以千变万化，穷举所有可行解其实并不是一件容易的事，需要你熟练掌握递归思维，只有列出**正确的「[[状态转移方程]]」**，才能正确地穷举。而且，你需要判断算法问题是否**具备「[[最优子结构]]」**，是否能够通过子问题的最值得到原问题的最值。另外，动态规划问题**存在「重叠子问题」**，如果暴力穷举的话效率会很低，所以需要你使用「备忘录」或者「DP table」来优化穷举过程，避免不必要的计算。

# 框架：

```python
# 自顶向下递归的动态规划
def dp(状态1, 状态2, ...):
    for 选择 in 所有可能的选择:
        # 此时的状态已经因为做了选择而改变
        result = 求最值(result, dp(状态1, 状态2, ...))
    return result

# 自底向上迭代的动态规划
# 初始化 base case
dp[0][0][...] = base case
# 进行状态转移
for 状态1 in 状态1的所有取值：
    for 状态2 in 状态2的所有取值：
        for ...
            dp[状态1][状态2][...] = 求最值(选择1，选择2...)
```

# 例子：

> [!example]
> 给你  `k`  种面值的硬币，面值分别为  `c1, c2 ... ck`，每种硬币的数量无限，再给一个总金额  `amount`，问你**最少**需要几枚硬币凑出这个金额，如果不可能凑出，算法返回 -1 。算法的函数签名如下：
>
> ```
> # coins 中是可选硬币面值，amount 是目标金额
> def coinChange(coins: List[int], amount: int) -> int:
> ```
>
> 比如说  `k = 3`，面值分别为 1，2，5，总金额  `amount = 11`。那么最少需要 3 枚硬币凑出，即 11 = 5 + 5 + 1。

这个问题是动态规划问题，满足具有最优子结构

> [!help]+
> 假设你有面值为  `1, 2, 5`  的硬币，你想求  `amount = 11`  时的最少硬币数（原问题），如果你知道凑出  `amount = 10, 9, 6`  的最少硬币数（子问题），你只需要把子问题的答案加一（再选一枚面值为  `1, 2, 5`  的硬币），求个最小值，就是原问题的答案。因为硬币的数量是没有限制的，所以子问题之间没有相互制，是互相独立的。

# 解法

那么
首先，列出状态转移方程：

1. **确定「状态」，也就是原问题和子问题中会变化的变量**。由于硬币数量无限，硬币的面额也是题目给定的，只有目标金额会不断地向 base case 靠近，所以唯一的「状态」就是目标金额  `amount`。

2. **确定「选择」，也就是导致「状态」产生变化的行为**。目标金额为什么变化呢，因为你在选择硬币，你每选择一枚硬币，就相当于减少了目标金额。所以说所有硬币的面值，就是你的「选择」。

3. **明确  `dp`  函数/数组的定义**。我们这里讲的是自顶向下的解法，所以会有一个递归的  `dp`  函数，一般来说函数的参数就是状态转移中会变化的量，也就是上面说到的「状态」；函数的返回值就是题目要求我们计算的量。就本题来说，状态只有一个，即「目标金额」，题目要求我们计算凑出目标金额所需的最少硬币数量。

```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        # 题目要求的最终结果是 dp(amount)
        return self.dp(coins, amount)

    # 定义：要凑出金额 n，至少要 dp(coins, n) 个硬币
    def dp(self, coins, amount):
        # base case
        if amount == 0:
            return 0
        if amount < 0:
            return -1

        res = float('inf')
        for coin in coins:
            # 计算子问题的结果
            subProblem = self.dp(coins, amount - coin)
            # 子问题无解则跳过
            if subProblem == -1:
                continue
            # 在子问题中选择最优解，然后加一
            res = min(res, subProblem + 1)

        return res if res != float('inf') else -1
```

# 优化：

## 带备忘录的解法：

```python
class Solution:
    def __init__(self):
        self.memo = []

    def coinChange(self, coins: List[int], amount: int) -> int:
        self.memo = [-666] * (amount + 1)
        # 备忘录初始化为一个不会被取到的特殊值，代表还未被计算
        return self.dp(coins, amount)

    def dp(self, coins, amount):
        if amount == 0: return 0
        if amount < 0: return -1
        # 查备忘录，防止重复计算
        if self.memo[amount] != -666:
            return self.memo[amount]

        res = float('inf')
        for coin in coins:
            # 计算子问题的结果
            subProblem = self.dp(coins, amount - coin)
            # 子问题无解则跳过
            if subProblem == -1: continue
            # 在子问题中选择最优解，然后加一
            res = min(res, subProblem + 1)
        # 把计算结果存入备忘录
        self.memo[amount] = res if res != float('inf') else -1
        return self.memo[amount]
```

## dp 数组迭代

当然，我们也可以自底向上使用 dp table 来消除重叠子问题，关于「状态」「选择」和 base case 与之前没有区别，`dp`  数组的定义和刚才  `dp`  函数类似，也是把「状态」，也就是目标金额作为变量。不过  `dp`  函数体现在函数参数，而  `dp`  数组体现在数组索引：
**`dp`  数组的定义：当目标金额为  `i`  时，至少需要  `dp[i]`  枚硬币凑出**。

```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        # 数组大小为 amount + 1，初始值也为 amount + 1
        dp = [amount + 1] * (amount + 1)

        dp[0] = 0
        # base case
        # 外层 for 循环在遍历所有状态的所有取值
        for i in range(len(dp)):
            # 内层 for 循环在求所有选择的最小值
            for coin in coins:
                # 子问题无解，跳过
                if i - coin < 0:
                    continue
                dp[i] = min(dp[i], 1 + dp[i - coin])
        return -1 if dp[amount] == amount + 1 else dp[amount]
```

> [!info]-
> 为啥  `dp`  数组中的值都初始化为  `amount + 1`  呢，因为凑成  `amount`  金额的硬币数最多只可能等于  `amount`（全用 1 元面值的硬币），所以初始化为  `amount + 1`  就相当于初始化为正无穷，便于后续取最小值。为啥不直接初始化为 int 型的最大值  `Integer.MAX_VALUE`  呢？因为后面有  `dp[i - coin] + 1`，这就会导致整型溢出。
