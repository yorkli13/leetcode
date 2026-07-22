# 72. 编辑距离 (Edit Distance)

## 题目描述

给你两个单词 `word1` 和 `word2`，请返回将 `word1` 转换成 `word2` 所使用的最少操作数。

你可以对一个单词进行以下三种操作：

1. 插入一个字符
2. 删除一个字符
3. 替换一个字符

**示例 1：**

```text
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse，将 h 替换为 r
rorse -> rose，删除 r
rose -> ros，删除 e
```

**示例 2：**

```text
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention，删除 t
inention -> enention，将 i 替换为 e
enention -> exention，将 n 替换为 x
exention -> exection，将 n 替换为 c
exection -> execution，插入 u
```

**提示：**

- `0 <= word1.length, word2.length <= 500`
- `word1` 和 `word2` 由小写英文字母组成

## 算法知识

### 动态规划

这题是字符串动态规划里的经典题。

它的难点在于：不能只看当前字符是否相同，还要考虑前面的部分已经用了多少步才能转换成功。

所以我们用动态规划保存子问题答案：

```text
dp[i][j] = 将 word1 的前 i 个字符转换成 word2 的前 j 个字符，所需的最少操作数
```

这样最终答案就是：

```text
dp[m][n]
```

其中 `m = word1.length()`，`n = word2.length()`。

## 解法：二维动态规划（推荐）

### 思路

设：

```text
word1 前 i 个字符 = word1[0...i-1]
word2 前 j 个字符 = word2[0...j-1]
```

我们要计算 `dp[i][j]`。

关键看最后一个字符：

```text
word1[i - 1]
word2[j - 1]
```

### 初始化

如果 `word2` 是空字符串，那么把 `word1` 的前 `i` 个字符变成空字符串，只能全部删除：

```text
dp[i][0] = i
```

如果 `word1` 是空字符串，那么把空字符串变成 `word2` 的前 `j` 个字符，只能不断插入：

```text
dp[0][j] = j
```

这就是第一行和第一列的含义。

### 情况 1：最后一个字符相同

如果：

```text
word1[i - 1] == word2[j - 1]
```

说明最后这个字符不用操作，只需要看前面的部分怎么转换：

```text
dp[i][j] = dp[i - 1][j - 1]
```

例如把 `"hor"` 变成 `"ros"` 时，如果最后字符都相同，就可以直接沿用前面子问题的答案。

### 情况 2：最后一个字符不同

如果：

```text
word1[i - 1] != word2[j - 1]
```

那我们有三种操作可以选择。

#### 1. 删除

删除 `word1` 的最后一个字符。

也就是先把 `word1` 前 `i - 1` 个字符变成 `word2` 前 `j` 个字符，然后再删除当前多出来的字符：

```text
dp[i - 1][j] + 1
```

#### 2. 插入

在 `word1` 后面插入一个字符，让它匹配 `word2[j - 1]`。

也就是先把 `word1` 前 `i` 个字符变成 `word2` 前 `j - 1` 个字符，然后再插入最后一个字符：

```text
dp[i][j - 1] + 1
```

#### 3. 替换

把 `word1[i - 1]` 直接替换成 `word2[j - 1]`。

也就是先把前面的部分转换好，再替换最后一个字符：

```text
dp[i - 1][j - 1] + 1
```

三种操作都可以做，我们当然选代价最小的：

```text
dp[i][j] = min(
    dp[i - 1][j],
    dp[i][j - 1],
    dp[i - 1][j - 1]
) + 1
```

### 为什么这样转移是对的

因为编辑操作最后一步只可能是三种之一：

- 最后删掉一个字符
- 最后插入一个字符
- 最后替换一个字符

而这三种情况刚好对应上面三个子问题。

我们把所有可能的最后一步都枚举出来，再取最小值，就不会漏掉最优解。

### 以 `word1 = "horse", word2 = "ros"` 为例

我们最终要算：

```text
dp[5][3]
```

表示把 `"horse"` 变成 `"ros"`。

其中一种最优过程是：

```text
horse -> rorse -> rose -> ros
```

一共 3 步。

动态规划不是只记这一条路径，而是把所有前缀之间的最小转换次数都算出来，最后自然得到 `dp[5][3] = 3`。

### 复杂度分析

- **时间复杂度：** `O(m * n)`，每个状态计算一次
- **空间复杂度：** `O(m * n)`，使用二维数组保存状态

## 代码

### C++

```cpp
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size();
        int n = word2.size();

        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

        for (int i = 0; i <= m; i++) {
            dp[i][0] = i;
        }
        for (int j = 0; j <= n; j++) {
            dp[0][j] = j;
        }

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1[i - 1] == word2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = min({
                        dp[i - 1][j],
                        dp[i][j - 1],
                        dp[i - 1][j - 1]
                    }) + 1;
                }
            }
        }

        return dp[m][n];
    }
};
```

### Python

```python
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        m, n = len(word1), len(word2)
        dp = [[0] * (n + 1) for _ in range(m + 1)]

        for i in range(m + 1):
            dp[i][0] = i
        for j in range(n + 1):
            dp[0][j] = j

        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if word1[i - 1] == word2[j - 1]:
                    dp[i][j] = dp[i - 1][j - 1]
                else:
                    dp[i][j] = min(
                        dp[i - 1][j],
                        dp[i][j - 1],
                        dp[i - 1][j - 1]
                    ) + 1

        return dp[m][n]
```
