---
comments: true
difficulty: 中等
edit_url: https://github.com/doocs/leetcode/edit/main/solution/0200-0299/0213.House%20Robber%20II/README.md
tags:
    - 数组
    - 动态规划
---

<!-- problem:start -->

# [213. 打家劫舍 II](https://leetcode.cn/problems/house-robber-ii)

[English Version](/solution/0200-0299/0213.House%20Robber%20II/README_EN.md)

## 题目描述

<!-- description:start -->

<p>你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 <strong>围成一圈</strong> ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，<strong>如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警</strong> 。</p>

<p>给定一个代表每个房屋存放金额的非负整数数组，计算你 <strong>在不触动警报装置的情况下</strong> ，今晚能够偷窃到的最高金额。</p>

<p>&nbsp;</p>

<p><strong>示例&nbsp;1：</strong></p>

<pre>
<strong>输入：</strong>nums = [2,3,2]
<strong>输出：</strong>3
<strong>解释：</strong>你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>nums = [1,2,3,1]
<strong>输出：</strong>4
<strong>解释：</strong>你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。
&nbsp;    偷窃到的最高金额 = 1 + 3 = 4 。</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>nums = [1,2,3]
<strong>输出：</strong>3
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>1 &lt;= nums.length &lt;= 100</code></li>
	<li><code>0 &lt;= nums[i] &lt;= 1000</code></li>
</ul>

<!-- description:end -->

## 解法

<!-- solution:start -->

### 方法一：动态规划

环状排列意味着第一个房屋和最后一个房屋中最多只能选择一个偷窃，因此可以把此环状排列房间问题约化为两个单排排列房屋子问题。

考虑是否偷 nums[0]：

如果偷 nums[0]，那么 nums[1] 和 nums[n−1] 不能偷，问题变成从 nums[2] 到 nums[n−2] 的非环形版本。这里官方题解计算的是 nums[0] 到 nums[n−2]，感觉绕了一个弯，不如直接求 nums[2] 到 nums[n−2]。
如果不偷 nums[0]，那么问题变成从 nums[1] 到 nums[n−1] 的非环形版本。
这两种方案覆盖了所有情况（毕竟 nums[0] 只有偷与不偷，没有第三种选择），所以取两种方案的最大值，即为答案。

此外，官方题解还特判了 n=1 和 n=2，其实这是没有必要的，两个变量初始化成 0 就行。

时间复杂度 $O(n)$，其中 $n$ 是数组长度。空间复杂度 $O(1)$。

<!-- tabs:start -->

#### Java

```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        if (n == 1) {
            return nums[0];
        }
        return Math.max(rob(nums, 0, n - 2), rob(nums, 1, n - 1));
    }

    private int rob(int[] nums, int start, int end) {
        int first = 0, second = 0;
        for (int i = start; i <= end; i++) {
            int temp = Math.max(first, second);
            second = first + nums[i];
            first = temp;
        }
        return Math.max(first, second);
    }
}
```

#### C++

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 1) {
            return nums[0];
        }
        return max(robRange(nums, 0, n - 2), robRange(nums, 1, n - 1));
    }

    int robRange(vector<int>& nums, int l, int r) {
        int f = 0, g = 0;
        for (; l <= r; ++l) {
            int ff = max(f, g);
            g = f + nums[l];
            f = ff;
        }
        return max(f, g);
    }
};
```

#### Go

```go
func rob(nums []int) int {
	n := len(nums)
	if n == 1 {
		return nums[0]
	}
	return max(robRange(nums, 0, n-2), robRange(nums, 1, n-1))
}

func robRange(nums []int, l, r int) int {
	f, g := 0, 0
	for _, x := range nums[l : r+1] {
		f, g = max(f, g), f+x
	}
	return max(f, g)
}
```

#### TypeScript

```ts
function rob(nums: number[]): number {
    const n = nums.length;
    if (n === 1) {
        return nums[0];
    }
    const robRange = (l: number, r: number): number => {
        let [f, g] = [0, 0];
        for (; l <= r; ++l) {
            [f, g] = [Math.max(f, g), f + nums[l]];
        }
        return Math.max(f, g);
    };
    return Math.max(robRange(0, n - 2), robRange(1, n - 1));
}
```

#### Rust

```rust
impl Solution {
    pub fn rob(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n == 1 {
            return nums[0];
        }
        let rob_range = |l, r| {
            let mut f = [0, 0];
            for i in l..r {
                f = [f[0].max(f[1]), f[0] + nums[i]];
            }
            f[0].max(f[1])
        };
        rob_range(0, n - 1).max(rob_range(1, n))
    }
}
```

<!-- tabs:end -->

<!-- solution:end -->

<!-- problem:end -->
