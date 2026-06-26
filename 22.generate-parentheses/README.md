# 22. 括号生成 (Generate Parentheses)

## 题目描述

数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且**有效的**括号组合。

**示例 1：**

```text
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

**示例 2：**

```text
输入：n = 1
输出：["()"]
```

**提示：**

- `1 <= n <= 8`

## 算法知识

### 回溯

回溯法适合解决“枚举所有合法结果”的问题。

本题需要生成所有可能的括号字符串，但并不是所有由 `n` 个左括号和 `n` 个右括号组成的字符串都合法。因此，我们不能先生成所有字符串再去筛选，而是应该在构造过程中就保证合法性。

这正是回溯法擅长的地方：

1. 每一步尝试添加一个字符
2. 如果当前状态仍然合法，就继续递归
3. 如果不合法，就立刻停止这条分支

### 有效括号的性质

一个有效括号串必须满足：

1. 左括号总数等于右括号总数
2. 在任意前缀中，右括号数量都不能超过左括号数量

例如：

- `"(()())"` 是合法的
- `")(()"` 一开始右括号就比左括号多，不合法

因此，回溯时只要维护“已经用了多少个左括号”和“已经用了多少个右括号”，就能控制生成过程。

## 解法：回溯生成所有合法括号（推荐）

### 思路

定义回溯函数，维护当前状态：

- `path`：当前已经构造出的字符串
- `left`：已经使用的左括号数量
- `right`：已经使用的右括号数量

在任意时刻：

1. 如果 `left < n`
   - 说明左括号还没用完
   - 可以继续添加 `'('`
2. 如果 `right < left`
   - 说明当前左括号数量更多
   - 可以添加 `')'`

递归终止条件是：

- 当 `path` 的长度等于 `2 * n` 时，说明已经构造出一个完整合法答案，可以加入结果集

### 为什么这样做可行？

因为这两个约束恰好保证了括号串始终朝着“合法答案”发展：

1. `left < n`
   - 保证左括号不会超过 `n` 个
2. `right < left`
   - 保证任何前缀中都不会出现右括号比左括号多的情况

这样生成出来的每条路径，最终一定是一个合法括号串；而所有合法括号串也都会被遍历到，因此不会漏解。

### 复杂度分析

- **时间复杂度**：与第 `n` 个卡特兰数相关，通常记作 O(Cn)。因为需要生成所有合法答案。
- **空间复杂度**：O(n)，递归深度和当前路径长度最多为 `2n`，若不计答案空间，则辅助空间为递归栈和构造路径所需空间。

## 代码

### C++

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> ans;
        string path;

        auto backtrack = [&](auto&& backtrack, int left, int right) -> void {
            if (path.size() == 2 * n) {
                ans.push_back(path);
                return;
            }

            if (left < n) {
                path.push_back('(');
                backtrack(backtrack, left + 1, right);
                path.pop_back();
            }

            if (right < left) {
                path.push_back(')');
                backtrack(backtrack, left, right + 1);
                path.pop_back();
            }
        };

        backtrack(backtrack, 0, 0);
        return ans;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        ans = []
        path = []

        def backtrack(left: int, right: int) -> None:
            if len(path) == 2 * n:
                ans.append("".join(path))
                return

            if left < n:
                path.append("(")
                backtrack(left + 1, right)
                path.pop()

            if right < left:
                path.append(")")
                backtrack(left, right + 1)
                path.pop()

        backtrack(0, 0)
        return ans
```
