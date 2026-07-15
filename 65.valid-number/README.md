# 65. 有效数字 (Valid Number)

## 题目描述

给你一个字符串 `s`，如果 `s` 是一个有效数字，请返回 `true`，否则返回 `false`。

一个有效数字可以分成以下几个部分：

1. 一个**整数**或者**小数**
2. 后面可以选择性跟一个指数部分 `e` 或 `E`

其中：

- 整数可以带前导符号 `+` 或 `-`
- 小数可以带前导符号 `+` 或 `-`
- 指数部分后面必须是一个整数

**示例 1：**

```text
输入：s = "0"
输出：true
```

**示例 2：**

```text
输入：s = "e"
输出：false
```

**示例 3：**

```text
输入：s = "."
输出：false
```

**提示：**

- `1 <= s.length <= 20`
- `s` 仅由英文字母（大小写）、数字（`0-9`）、加号 `+`、减号 `-`、小数点 `.` 组成

## 算法知识

### 字符串规则解析

这题本质上不是数学题，而是一个“字符串是否合法”的判断题。

关键不在于把字符串真的转换成数字，而在于判断它是否满足数字书写规则。

所以更自然的思路是：

1. 从左到右扫描字符串
2. 遇到不同字符时，判断它是否合法
3. 用几个布尔变量记录关键状态

这种做法比写一堆零散特判更清楚，也更适合复用到类似解析题里。

## 解法：用状态标记逐字符判断（推荐）

### 思路

我们从左到右扫描字符串，并维护三个核心状态：

- `seenDigit`：前面是否已经出现过数字
- `seenDot`：前面是否已经出现过小数点
- `seenExp`：前面是否已经出现过指数 `e` 或 `E`

然后分类讨论每个字符。

### 1. 如果当前字符是数字

那它一定是合法的，可以直接继续。

同时说明我们已经见过数字了：

```text
seenDigit = true
```

### 2. 如果当前字符是 `+` 或 `-`

符号位不是随便哪里都能出现。

它只能出现在：

1. 字符串开头
2. `e` 或 `E` 的后面

所以如果它不在这两个位置，就不合法。

也就是说，当前字符是符号时，必须满足：

```text
i == 0
或者
s[i - 1] 是 'e' 或 'E'
```

### 3. 如果当前字符是 `.`

小数点也有限制：

1. 不能出现两次
2. 不能出现在指数部分后面

所以如果前面已经出现过小数点，或者已经出现过 `e/E`，那么当前 `.` 就不合法。

否则可以标记：

```text
seenDot = true
```

### 4. 如果当前字符是 `e` 或 `E`

指数部分最容易出错，规则有两个关键点：

1. `e/E` 前面必须已经有数字
2. `e/E` 只能出现一次

比如：

- `"e3"` 不合法，因为前面没有数字
- `"1e2e3"` 不合法，因为指数出现了两次

如果当前 `e/E` 合法，那么要注意一件事：

指数后面还必须再出现数字。

所以可以先把：

```text
seenDigit = false
```

重置掉，表示“后半段的数字还没看到，后面必须补上”。

同时标记：

```text
seenExp = true
```

### 5. 如果是其他字符

那就直接不合法，返回 `false`。

### 最后为什么返回 `seenDigit`

因为扫描结束时，我们必须确保最后这一段是完整的数字。

尤其是处理过 `e/E` 之后，`seenDigit` 会被重置成 `false`，只有当指数后面真正出现数字时，它才会重新变成 `true`。

所以最后：

```text
return seenDigit
```

就能保证像这些情况被正确排除：

- `"1e"` 不合法
- `"+"` 不合法
- `"."` 不合法

### 以 `"53.5e93"` 为例

扫描过程：

1. `5` 是数字，`seenDigit = true`
2. `3` 是数字，继续
3. `.` 是小数点，前面没点也没指数，合法
4. `5` 是数字，继续
5. `e` 前面已有数字且没出现过指数，合法
   - `seenExp = true`
   - `seenDigit = false`
6. `9` 是数字，`seenDigit = true`
7. `3` 是数字，继续

最后结束时 `seenDigit = true`，所以整个字符串合法。

### 复杂度分析

- **时间复杂度：** `O(n)`，只扫描一次字符串
- **空间复杂度：** `O(1)`，只使用常数个状态变量

## 代码

### C++

```cpp
#include <string>
using namespace std;

class Solution {
public:
    bool isNumber(string s) {
        bool seenDigit = false;
        bool seenDot = false;
        bool seenExp = false;

        for (int i = 0; i < s.size(); i++) {
            char ch = s[i];

            if (isdigit(ch)) {
                seenDigit = true;
            } else if (ch == '+' || ch == '-') {
                if (i > 0 && s[i - 1] != 'e' && s[i - 1] != 'E') {
                    return false;
                }
            } else if (ch == '.') {
                if (seenDot || seenExp) {
                    return false;
                }
                seenDot = true;
            } else if (ch == 'e' || ch == 'E') {
                if (seenExp || !seenDigit) {
                    return false;
                }
                seenExp = true;
                seenDigit = false;
            } else {
                return false;
            }
        }

        return seenDigit;
    }
};
```

### Python

```python
class Solution:
    def isNumber(self, s: str) -> bool:
        seen_digit = False
        seen_dot = False
        seen_exp = False

        for i, ch in enumerate(s):
            if ch.isdigit():
                seen_digit = True
            elif ch in "+-":
                if i > 0 and s[i - 1] not in "eE":
                    return False
            elif ch == ".":
                if seen_dot or seen_exp:
                    return False
                seen_dot = True
            elif ch in "eE":
                if seen_exp or not seen_digit:
                    return False
                seen_exp = True
                seen_digit = False
            else:
                return False

        return seen_digit
```
