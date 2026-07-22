# 76. 最小覆盖子串 (Minimum Window Substring)

## 题目描述

给你两个字符串 `s` 和 `t`。

请你在 `s` 中找出最短的子串，使得这个子串包含 `t` 中所有字符，包括重复字符。

如果 `s` 中不存在这样的子串，返回空字符串 `""`。

**示例 1：**

```text
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
解释：最小覆盖子串 "BANC" 包含了 'A'、'B'、'C'
```

**示例 2：**

```text
输入：s = "a", t = "a"
输出："a"
```

**示例 3：**

```text
输入：s = "a", t = "aa"
输出：""
解释：t 中有两个 'a'，但 s 中只有一个 'a'
```

**提示：**

- `1 <= s.length, t.length <= 10^5`
- `s` 和 `t` 由英文字母组成
- 答案唯一

## 算法知识

### 滑动窗口

这题是滑动窗口里的经典题。

我们要找的是一个连续子串，并且这个子串要满足：

```text
包含 t 中所有字符及其出现次数
```

对于这种“连续区间 + 满足某种字符频次条件 + 求最短”的问题，通常可以用滑动窗口。

滑动窗口的基本思路是：

1. 右指针不断向右扩张，让窗口逐渐包含足够字符
2. 当窗口已经满足条件时，左指针向右收缩，尽量缩短窗口
3. 在每次满足条件时更新答案

## 解法：滑动窗口 + 字符计数（推荐）

### 思路

我们先统计 `t` 中每个字符需要出现多少次。

例如：

```text
t = "AABC"
```

那么需要：

```text
A: 2
B: 1
C: 1
```

然后用窗口在 `s` 上移动，同时统计窗口里已经包含了多少需要的字符。

### 需要维护哪些变量

我们维护两个计数数组：

- `need`：记录 `t` 中每个字符需要多少个
- `window`：记录当前窗口中每个字符有多少个

再维护一个变量：

```text
formed
```

表示当前窗口中，有多少种字符已经满足了 `t` 的要求。

还需要一个：

```text
required
```

表示 `t` 中一共有多少种不同字符需要满足。

当：

```text
formed == required
```

就说明当前窗口已经覆盖了 `t`。

### 右指针：扩大窗口

右指针 `right` 从左到右扫描 `s`。

每加入一个字符 `ch = s[right]`：

1. 把它加入窗口计数
2. 如果它是 `t` 中需要的字符，并且窗口里的数量刚好达到需要数量
3. 那么 `formed++`

这表示又有一种字符满足要求了。

### 左指针：缩小窗口

当：

```text
formed == required
```

说明当前窗口已经合法。

这时就尝试不断移动左指针 `left`，把窗口缩小。

每次缩小前，先更新最短答案。

然后移出 `s[left]`：

1. 窗口计数减一
2. 如果这个字符是需要的字符，并且移出后数量不足
3. 那么 `formed--`

这表示窗口不再满足条件，停止收缩，继续让右指针扩张。

### 为什么不是只看字符种类，还要看数量

因为 `t` 里可能有重复字符。

比如：

```text
t = "aa"
```

窗口里只有一个 `a` 是不够的，必须有两个 `a`。

所以判断条件不能只是“有没有这个字符”，还要看：

```text
window[ch] >= need[ch]
```

是否满足对应数量。

### 以示例 1 为例

```text
s = "ADOBECODEBANC"
t = "ABC"
```

`need` 是：

```text
A: 1
B: 1
C: 1
```

右指针向右扩张，第一次得到合法窗口：

```text
"ADOBEC"
```

它包含了 `A`、`B`、`C`，所以合法。

接着左指针开始收缩。

后面继续移动右指针，最终会找到更短的合法窗口：

```text
"BANC"
```

长度是 4，是最短答案。

### 为什么滑动窗口不会漏掉答案

因为每个右端点固定时，我们都会尽量向右移动左端点，直到窗口刚好不能再满足条件。

也就是说，对于每个可能的右端点，我们都尝试找到了最短的合法左端点。

整个过程中：

- `right` 只向右走
- `left` 也只向右走

所以既不会漏掉最优答案，也不会退回重复检查。

### 复杂度分析

- **时间复杂度：** `O(n + m)`，其中 `n = s.length()`，`m = t.length()`
- **空间复杂度：** `O(1)`，因为英文字母字符集大小有限；如果按一般字符集理解，就是 `O(C)`

## 代码

### C++

```cpp
#include <string>
#include <vector>
#include <climits>
using namespace std;

class Solution {
public:
    string minWindow(string s, string t) {
        vector<int> need(128, 0);
        vector<int> window(128, 0);

        for (char ch : t) {
            need[ch]++;
        }

        int required = 0;
        for (int count : need) {
            if (count > 0) {
                required++;
            }
        }

        int formed = 0;
        int left = 0;
        int bestLen = INT_MAX;
        int bestStart = 0;

        for (int right = 0; right < s.size(); right++) {
            char ch = s[right];
            window[ch]++;

            if (need[ch] > 0 && window[ch] == need[ch]) {
                formed++;
            }

            while (formed == required) {
                if (right - left + 1 < bestLen) {
                    bestLen = right - left + 1;
                    bestStart = left;
                }

                char leftChar = s[left];
                window[leftChar]--;
                if (need[leftChar] > 0 && window[leftChar] < need[leftChar]) {
                    formed--;
                }
                left++;
            }
        }

        if (bestLen == INT_MAX) {
            return "";
        }
        return s.substr(bestStart, bestLen);
    }
};
```

### Python

```python
from collections import Counter, defaultdict


class Solution:
    def minWindow(self, s: str, t: str) -> str:
        need = Counter(t)
        window = defaultdict(int)
        required = len(need)
        formed = 0

        left = 0
        best_len = float("inf")
        best_start = 0

        for right, ch in enumerate(s):
            window[ch] += 1

            if ch in need and window[ch] == need[ch]:
                formed += 1

            while formed == required:
                if right - left + 1 < best_len:
                    best_len = right - left + 1
                    best_start = left

                left_ch = s[left]
                window[left_ch] -= 1
                if left_ch in need and window[left_ch] < need[left_ch]:
                    formed -= 1
                left += 1

        if best_len == float("inf"):
            return ""
        return s[best_start:best_start + best_len]
```
