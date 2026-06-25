# 17. 电话号码的字母组合 (Letter Combinations of a Phone Number)

## 题目描述

给定一个仅包含数字 `2-9` 的字符串 `digits`，返回它能表示的所有字母组合。

答案可以按任意顺序返回。

数字到字母的映射如下（与电话按键相同）：

```text
2 -> "abc"
3 -> "def"
4 -> "ghi"
5 -> "jkl"
6 -> "mno"
7 -> "pqrs"
8 -> "tuv"
9 -> "wxyz"
```

注意，数字 `1` 不对应任何字母。

**示例 1：**

```text
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**示例 2：**

```text
输入：digits = ""
输出：[]
```

**示例 3：**

```text
输入：digits = "2"
输出：["a","b","c"]
```

**提示：**

- `0 <= digits.length <= 4`
- `digits[i]` 是范围 `['2', '9']` 的一个数字

## 算法知识

### 回溯

回溯法适合处理“列举所有可能结果”的问题，尤其是组合、排列、子集这类题目。

它的核心思想是：

1. 先选择一个当前可行的选项
2. 继续递归处理下一步
3. 递归返回后撤销本次选择，再尝试别的选项

本题中，每个数字都对应若干个字母，我们需要从每一位数字对应的字母集合中各选一个，最终组成所有可能的字符串，这正是回溯的典型应用场景。

### 映射关系

本题需要先建立数字到字母的映射，例如：

- `'2' -> "abc"`
- `'3' -> "def"`
- `'4' -> "ghi"`
- `'5' -> "jkl"`
- `'6' -> "mno"`
- `'7' -> "pqrs"`
- `'8' -> "tuv"`
- `'9' -> "wxyz"`

然后按顺序处理输入字符串中的每一位数字。

## 解法：回溯枚举所有组合（推荐）

### 思路

把问题看成一棵决策树：

- 第 1 层从第 1 个数字对应的字母中选一个
- 第 2 层从第 2 个数字对应的字母中选一个
- ...
- 当选满 `digits.length` 个字符时，就得到一个完整组合

例如 `digits = "23"`：

- 数字 `2` 对应 `"abc"`
- 数字 `3` 对应 `"def"`

组合过程如下：

- 先选 `a`，再接 `d/e/f`，得到 `ad/ae/af`
- 再选 `b`，再接 `d/e/f`，得到 `bd/be/bf`
- 再选 `c`，再接 `d/e/f`，得到 `cd/ce/cf`

### 递归过程

定义递归函数 `backtrack(index)`，表示当前正在处理 `digits[index]`：

1. 如果 `index == digits.length()`，说明已经构造出一个完整字符串
   - 把当前路径加入答案
2. 否则，取出当前数字对应的所有字母
3. 依次尝试每个字母：
   - 加入当前路径
   - 递归处理下一位数字
   - 回溯，撤销刚才加入的字母

### 为什么空字符串要返回空数组？

如果输入是空字符串，说明没有任何数字，自然也就没有任何组合。

注意这里返回的是：

```text
[]
```

而不是：

```text
[""]
```

因为题目要的是“可组成的字母组合”，空输入不应该产生一个空串作为有效组合。

### 复杂度分析

- **时间复杂度**：O(4^n * n)，其中 `n` 是 `digits` 的长度。每一位最多有 4 种选择，生成每个结果时还需要复制长度为 `n` 的字符串。
- **空间复杂度**：O(n)，递归栈和当前路径长度最多为 `n`。若计入答案空间，则还需要加上结果集本身的空间。

## 代码

### C++

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    vector<string> letterCombinations(string digits) {
        if (digits.empty()) {
            return {};
        }

        vector<string> mapping = {
            "",    "",    "abc",  "def", "ghi",
            "jkl", "mno", "pqrs", "tuv", "wxyz"
        };

        vector<string> ans;
        string path;

        auto backtrack = [&](auto&& backtrack, int index) -> void {
            if (index == digits.size()) {
                ans.push_back(path);
                return;
            }

            string letters = mapping[digits[index] - '0'];
            for (char ch : letters) {
                path.push_back(ch);
                backtrack(backtrack, index + 1);
                path.pop_back();
            }
        };

        backtrack(backtrack, 0);
        return ans;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        if not digits:
            return []

        mapping = {
            "2": "abc", "3": "def", "4": "ghi", "5": "jkl",
            "6": "mno", "7": "pqrs", "8": "tuv", "9": "wxyz"
        }

        ans = []
        path = []

        def backtrack(index: int) -> None:
            if index == len(digits):
                ans.append("".join(path))
                return

            for ch in mapping[digits[index]]:
                path.append(ch)
                backtrack(index + 1)
                path.pop()

        backtrack(0)
        return ans
```
