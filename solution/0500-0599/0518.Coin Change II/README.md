---
comments: true
difficulty: 中等
edit_url: https://github.com/doocs/leetcode/edit/main/solution/0500-0599/0518.Coin%20Change%20II/README.md
tags:
    - 数组
    - 动态规划
---

<!-- problem:start -->

# [518. 零钱兑换 II](https://leetcode.cn/problems/coin-change-ii)

[English Version](/solution/0500-0599/0518.Coin%20Change%20II/README_EN.md)

## 题目描述

<!-- description:start -->

<p>给你一个整数数组 <code>coins</code> 表示不同面额的硬币，另给一个整数 <code>amount</code> 表示总金额。</p>

<p>请你计算并返回可以凑成总金额的硬币组合数。如果任何硬币组合都无法凑出总金额，返回 <code>0</code> 。</p>

<p>假设每一种面额的硬币有无限个。 </p>

<p>题目数据保证结果符合 32 位带符号整数。</p>

<p> </p>

<ul>
</ul>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>amount = 5, coins = [1, 2, 5]
<strong>输出：</strong>4
<strong>解释：</strong>有四种方式可以凑成总金额：
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>amount = 3, coins = [2]
<strong>输出：</strong>0
<strong>解释：</strong>只用面额 2 的硬币不能凑成总金额 3 。
</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>amount = 10, coins = [10] 
<strong>输出：</strong>1
</pre>

<p> </p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>1 <= coins.length <= 300</code></li>
	<li><code>1 <= coins[i] <= 5000</code></li>
	<li><code>coins</code> 中的所有值 <strong>互不相同</strong></li>
	<li><code>0 <= amount <= 5000</code></li>
</ul>

<!-- description:end -->

## 解法

<!-- solution:start -->

### 方法一：动态规划 1-D

可以通过动态规划的方法计算可能的组合数。用 dp[x] 表示金额之和等于 x 的硬币组合数，目标是求 dp[amount]。

动态规划的边界是 dp[0]=1。只有当不选取任何硬币时，金额之和才为 0，因此只有 1 种硬币组合。

对于面额为 coin 的硬币，当 coin≤i≤amount 时，如果存在一种硬币组合的金额之和等于 i−coin，则在该硬币组合中增加一个面额为 coin 的硬币，即可得到一种金额之和等于 i 的硬币组合。因此需要遍历 coins，对于其中的每一种面额的硬币，更新数组 dp 中的每个大于或等于该面额的元素的值。

由此可以得到动态规划的做法：

初始化 dp[0]=1；

遍历 coins，对于其中的每个元素 coin，进行如下操作：

遍历 i 从 coin 到 amount，将 dp[i−coin] 的值加到 dp[i]。
最终得到 dp[amount] 的值即为答案。

#### Java

```java
class Solution {
    public int change(int amount, int[] coins) {
        int[] dp = new int[amount + 1];
        boolean[] valid = new boolean[amount + 1];
        dp[0] = 1;
        valid[0] = true;
        for (int coin : coins) {
            for (int i = coin; i <= amount; i++) {
                valid[i] |= valid[i - coin];
            }
        }
        if(!valid[amount]) return 0;
        for (int coin : coins) {
            for (int i = coin; i <= amount; i++) {
                dp[i] += dp[i - coin];
            }
        }
        return dp[amount];
    }
}

作者：力扣官方题解
链接：https://leetcode.cn/problems/coin-change-ii/solutions/821278/ling-qian-dui-huan-ii-by-leetcode-soluti-f7uh/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

### 方法一：动态规划(完全背包) 2-D

我们定义 $f[i][j]$ 表示使用前 $i$ 种硬币，凑出金额 $j$ 的硬币组合数。初始时 $f[0][0] = 1$，其余位置的值均为 $0$。

我们可以枚举使用的最后一枚硬币的数量 $k$，那么有式子一：

$$
f[i][j] = f[i - 1][j] + f[i - 1][j - x] + f[i - 1][j - 2 \times x] + \cdots + f[i - 1][j - k \times x]
$$

其中 $x$ 表示第 $i$ 种硬币的面值。

不妨令 $j = j - x$，那么有式子二：

$$
f[i][j - x] = f[i - 1][j - x] + f[i - 1][j - 2 \times x] + \cdots + f[i - 1][j - k \times x]
$$

将式子二代入式子一，得到：

$$
f[i][j] = f[i - 1][j] + f[i][j - x]
$$

最终的答案为 $f[m][n]$，其中 $m$ 和 $n$ 分别表示硬币的种类数和总金额。

时间复杂度 $O(m \times n)$，空间复杂度 $O(m \times n)$。其中 $m$ 和 $n$ 分别为硬币的种类数和总金额。

<!-- tabs:start -->

#### Java

```java
class Solution {
    public int change(int amount, int[] coins) {
        int m = coins.length, n = amount;
        int[][] f = new int[m + 1][n + 1];
        f[0][0] = 1;
        for (int i = 1; i <= m; ++i) {
            for (int j = 0; j <= n; ++j) {
                f[i][j] = f[i - 1][j];
                if (j >= coins[i - 1]) {
                    f[i][j] += f[i][j - coins[i - 1]];
                }
            }
        }
        return f[m][n];
    }
}
```

#### C++

```cpp
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int m = coins.size(), n = amount;
        unsigned f[m + 1][n + 1];
        memset(f, 0, sizeof(f));
        f[0][0] = 1;
        for (int i = 1; i <= m; ++i) {
            for (int j = 0; j <= n; ++j) {
                f[i][j] = f[i - 1][j];
                if (j >= coins[i - 1]) {
                    f[i][j] += f[i][j - coins[i - 1]];
                }
            }
        }
        return f[m][n];
    }
};
```

#### Go

```go
func change(amount int, coins []int) int {
	m, n := len(coins), amount
	f := make([][]int, m+1)
	for i := range f {
		f[i] = make([]int, n+1)
	}
	f[0][0] = 1
	for i := 1; i <= m; i++ {
		for j := 0; j <= n; j++ {
			f[i][j] = f[i-1][j]
			if j >= coins[i-1] {
				f[i][j] += f[i][j-coins[i-1]]
			}
		}
	}
	return f[m][n]
}
```

#### TypeScript

```ts
function change(amount: number, coins: number[]): number {
    const [m, n] = [coins.length, amount];
    const f: number[][] = Array.from({ length: m + 1 }, () => Array(n + 1).fill(0));
    f[0][0] = 1;
    for (let i = 1; i <= m; ++i) {
        for (let j = 0; j <= n; ++j) {
            f[i][j] = f[i - 1][j];
            if (j >= coins[i - 1]) {
                f[i][j] += f[i][j - coins[i - 1]];
            }
        }
    }
    return f[m][n];
}
```

<!-- tabs:end -->

我们注意到 $f[i][j]$ 只与 $f[i - 1][j]$ 和 $f[i][j - x]$ 有关，因此我们可以将二维数组优化为一维数组，空间复杂度降为 $O(n)$。

<!-- tabs:start -->

#### Python3

```python
class Solution:
    def change(self, amount: int, coins: List[int]) -> int:
        n = amount
        f = [1] + [0] * n
        for x in coins:
            for j in range(x, n + 1):
                f[j] += f[j - x]
        return f[n]
```

#### Java

```java
class Solution {
    public int change(int amount, int[] coins) {
        int n = amount;
        int[] f = new int[n + 1];
        f[0] = 1;
        for (int x : coins) {
            for (int j = x; j <= n; ++j) {
                f[j] += f[j - x];
            }
        }
        return f[n];
    }
}
```

#### C++

```cpp
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int n = amount;
        unsigned f[n + 1];
        memset(f, 0, sizeof(f));
        f[0] = 1;
        for (int x : coins) {
            for (int j = x; j <= n; ++j) {
                f[j] += f[j - x];
            }
        }
        return f[n];
    }
};
```

#### Go

```go
func change(amount int, coins []int) int {
	n := amount
	f := make([]int, n+1)
	f[0] = 1
	for _, x := range coins {
		for j := x; j <= n; j++ {
			f[j] += f[j-x]
		}
	}
	return f[n]
}
```

#### TypeScript

```ts
function change(amount: number, coins: number[]): number {
    const n = amount;
    const f: number[] = Array(n + 1).fill(0);
    f[0] = 1;
    for (const x of coins) {
        for (let j = x; j <= n; ++j) {
            f[j] += f[j - x];
        }
    }
    return f[n];
}
```

<!-- tabs:end -->

<!-- solution:end -->

<!-- problem:end -->
