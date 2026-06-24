# 6. Z 字形变换

## 题目描述

将一个给定字符串 `s` 根据给定的行数 `numRows`，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 `"PAYPALISHIRING"` 行数为 `3` 时，排列如下：

```
P   A   H   N
A P L S I I G
Y   I   R
```

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"PAHNAPLSIIGYIR"`。

请你实现这个将字符串进行指定行数变换的函数：

```
string convert(string s, int numRows);
```

**示例 1：**

```
输入：s = "PAYPALISHIRING", numRows = 3
输出："PAHNAPLSIIGYIR"
```

**示例 2：**

```
输入：s = "PAYPALISHIRING", numRows = 4
输出："PINALSIGYAHRPI"
解释：
P     I    N
A   L S  I G
Y A   H R
P     I
```

**示例 3：**

```
输入：s = "A", numRows = 1
输出："A"
```

**提示：**

- `1 <= s.length <= 1000`
- `s` 由英文字母（小写和大写）、`','` 和 `'.'` 组成
- `1 <= numRows <= 1000`

## 算法知识

### 模拟法

核心思想是模拟 Z 字形排列的过程。维护 `numRows` 个字符串（每行一个），以及一个方向变量控制当前字符应该放到哪一行。

遍历字符串中的每个字符：
1. 将字符追加到当前行
2. 如果到达第 0 行或第 `numRows-1` 行（拐点），反转方向
3. 根据方向移动到下一行或上一行

当 `numRows = 1` 时，过程退化为单行，直接返回原字符串。

**时间复杂度**：O(n)，其中 n 是字符串长度，每个字符只处理一次。

**空间复杂度**：O(n)，用于存储每行的字符。

### 直接按行访问（数学法）

观察 Z 字形排列的规律，可以发现一个周期长度为 `cycle = 2 * (numRows - 1)`（当 `numRows > 1` 时）。

- 第 0 行：索引为 `0, cycle, 2*cycle, ...`
- 第 `numRows-1` 行：索引为 `cycle-1, 2*cycle-1, ...`
- 中间行 i：每周期有两个字符，索引为 `i + k*cycle` 和 `(cycle - i) + k*cycle`

这种方法可以跳过模拟过程，直接按行计算字符在原字符串中的位置，效率相同但实现更精巧。

## 解法

### 模拟法（推荐）

用 `rows` 数组存储每行的字符，方向变量 `direction`（1 向下，-1 向上）控制行号变化，遍历字符串依次放入对应行。

### 复杂度

- 时间复杂度：O(n)
- 空间复杂度：O(n)

## 代码

### C++

```cpp
#include <string>
#include <vector>

class Solution {
public:
    std::string convert(std::string s, int numRows) {
        if (numRows == 1) return s;

        std::vector<std::string> rows(numRows);
        int row = 0;
        int direction = -1;

        for (char c : s) {
            rows[row] += c;
            if (row == 0 || row == numRows - 1) {
                direction = -direction;
            }
            row += direction;
        }

        std::string result;
        for (const std::string& r : rows) {
            result += r;
        }
        return result;
    }
};
```

### Python

```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s

        rows = [""] * numRows
        row = 0
        direction = -1

        for c in s:
            rows[row] += c
            if row == 0 or row == numRows - 1:
                direction = -direction
            row += direction

        return "".join(rows)
```
