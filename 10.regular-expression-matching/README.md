# 10. 正则表达式匹配 (Regular Expression Matching)

## 题目描述

给你一个字符串 `s` 和一个字符规律 `p`，请你来实现一个支持 `'.'` 和 `'*'` 的正则表达式匹配。

其中：

- `'.'` 匹配任意单个字符
- `'*'` 匹配零个或多个前面的那一个元素

所谓匹配，是指要**覆盖整个字符串** `s`，而不是部分匹配。

**示例 1：**

```text
输入：s = "aa", p = "a"
输出：false
解释："a" 无法匹配整个字符串 "aa"。
```

**示例 2：**

```text
输入：s = "aa", p = "a*"
输出：true
解释：因为 '*' 表示前面的元素可以出现零次或多次，所以 "a*" 可以匹配 "aa"。
```

**示例 3：**

```text
输入：s = "ab", p = ".*"
输出：true
解释：'.' 匹配任意字符，'*' 表示前面的 '.' 可以重复任意次，所以可以匹配任意字符串。
```

**提示：**

- `1 <= s.length <= 20`
- `1 <= p.length <= 20`
- `s` 只包含从 `a-z` 的小写字母
- `p` 只包含从 `a-z` 的小写字母，以及字符 `'.'` 和 `'*'`
- 保证每次出现字符 `'*'` 时，前面都匹配到有效的字符

## 算法知识

### 动态规划

动态规划适合处理“一个大问题可以拆成多个重复子问题”的场景。本题中，我们要判断字符串 `s` 的前若干个字符，是否能被模式串 `p` 的前若干个字符匹配，这正好可以拆成子问题。

通常我们定义一个二维状态：

- `dp[i][j]` 表示 `s` 的前 `i` 个字符，是否能被 `p` 的前 `j` 个字符匹配

最终答案就是 `dp[m][n]`，其中 `m = s.length()`，`n = p.length()`。

### `.` 和 `*` 的含义

本题最关键的是理解模式字符的作用：

- 普通字符：必须和 `s` 中当前字符完全相同
- `.`：可以匹配任意一个字符
- `*`：不能单独使用，它总是修饰前一个字符，表示“前一个字符出现 0 次或多次”

例如：

- `"a*"` 可以匹配 `""`、`"a"`、`"aa"`、`"aaa"`
- `".*"` 可以匹配任意长度的任意字符串

## 解法：动态规划（推荐）

### 思路

定义 `dp[i][j]` 表示：

- `s[0..i-1]` 是否能和 `p[0..j-1]` 完全匹配

先看初始状态：

- `dp[0][0] = true`，空字符串和空模式可以匹配
- 对于空字符串 `s`，如果模式形如 `"a*b*c*"`，也可能匹配成功，因此需要提前初始化第一行

然后分情况讨论转移：

1. 如果 `p[j-1]` 是普通字符或 `'.'`
   - 只要当前字符能匹配，并且前面的部分也匹配，就有：
   - `dp[i][j] = dp[i-1][j-1]`

2. 如果 `p[j-1]` 是 `'*'`
   - `'*'` 修饰的是 `p[j-2]`
   - 有两种选择：

   第一种：把 `p[j-2]*` 看成出现 `0` 次
   - 直接忽略这两个字符
   - `dp[i][j] = dp[i][j-2]`

   第二种：把 `p[j-2]*` 看成出现至少 `1` 次
   - 前提是 `s[i-1]` 能和 `p[j-2]` 匹配
   - 如果能匹配，则继续看 `s` 去掉一个字符后，是否仍能匹配当前模式：
   - `dp[i][j] = dp[i-1][j]`

把两种情况取“或”即可。

### 状态转移

定义辅助函数 `matches(i, j)` 表示：

- `s[i-1]` 是否能和 `p[j-1]` 匹配

则有：

1. 当 `p[j-1] != '*'` 时：

```text
如果 matches(i, j) 为 true：
    dp[i][j] = dp[i-1][j-1]
```

2. 当 `p[j-1] == '*'` 时：

```text
dp[i][j] = dp[i][j-2]                         // 匹配 0 次
如果 matches(i, j-1) 为 true：
    dp[i][j] |= dp[i-1][j]                    // 匹配 >= 1 次
```

注意这里判断的是 `j-1`，因为 `'*'` 修饰的是它前面的那个字符 `p[j-2]`。

### 复杂度分析

- **时间复杂度**：O(mn)，其中 `m` 是字符串 `s` 的长度，`n` 是模式串 `p` 的长度。
- **空间复杂度**：O(mn)，使用了一个二维动态规划数组。

## 代码

### C++

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    bool isMatch(string s, string p) {
        int m = s.size(), n = p.size();
        vector<vector<bool>> dp(m + 1, vector<bool>(n + 1, false));
        dp[0][0] = true;

        auto matches = [&](int i, int j) -> bool {
            if (i == 0) {
                return false;
            }
            if (p[j - 1] == '.') {
                return true;
            }
            return s[i - 1] == p[j - 1];
        };

        // 初始化空字符串和模式串的匹配情况
        for (int j = 2; j <= n; j++) {
            if (p[j - 1] == '*') {
                dp[0][j] = dp[0][j - 2];
            }
        }

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (p[j - 1] == '*') {
                    // 匹配 0 次：忽略前一个字符和 *
                    dp[i][j] = dp[i][j - 2];
                    // 匹配 >= 1 次：当前字符能和 * 前面的字符匹配
                    if (matches(i, j - 1)) {
                        dp[i][j] = dp[i][j] || dp[i - 1][j];
                    }
                } else {
                    if (matches(i, j)) {
                        dp[i][j] = dp[i - 1][j - 1];
                    }
                }
            }
        }

        return dp[m][n];
    }
};
```

### Python

```python
from typing import List


class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        m, n = len(s), len(p)
        dp = [[False] * (n + 1) for _ in range(m + 1)]
        dp[0][0] = True

        def matches(i: int, j: int) -> bool:
            if i == 0:
                return False
            if p[j - 1] == ".":
                return True
            return s[i - 1] == p[j - 1]

        # 初始化空字符串和模式串的匹配情况
        for j in range(2, n + 1):
            if p[j - 1] == "*":
                dp[0][j] = dp[0][j - 2]

        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if p[j - 1] == "*":
                    # 匹配 0 次：忽略前一个字符和 *
                    dp[i][j] = dp[i][j - 2]
                    # 匹配 >= 1 次：当前字符能和 * 前面的字符匹配
                    if matches(i, j - 1):
                        dp[i][j] = dp[i][j] or dp[i - 1][j]
                else:
                    if matches(i, j):
                        dp[i][j] = dp[i - 1][j - 1]

        return dp[m][n]
```
