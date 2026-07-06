# 43. 字符串相乘 (Multiply Strings)

## 题目描述

给定两个以字符串形式表示的非负整数 `num1` 和 `num2`，返回它们的乘积，乘积也用字符串形式表示。

注意：

- 不能使用任何内置的大整数类型
- 也不能直接将输入转换为整数来处理

**示例 1：**

```text
输入：num1 = "2", num2 = "3"
输出："6"
```

**示例 2：**

```text
输入：num1 = "123", num2 = "456"
输出："56088"
```

**提示：**

- `1 <= num1.length, num2.length <= 200`
- `num1` 和 `num2` 只包含数字字符
- `num1` 和 `num2` 都不包含除数字 `0` 本身外的前导零

## 算法知识

### 模拟竖式乘法

这题本质上就是把我们手算乘法的过程写成代码。

例如：

```text
  123
x 456
-----
```

我们平时是怎么做的？

1. 用 `6` 去乘 `123`
2. 用 `5` 去乘 `123`
3. 用 `4` 去乘 `123`
4. 把它们按位对齐后加起来

代码里也完全可以这样模拟。

### 结果位数的规律

如果：

- `num1` 长度是 `m`
- `num2` 长度是 `n`

那么它们的乘积长度最多是：

```text
m + n
```

例如：

- 99 × 99 = 9801，长度是 4
- 2 位数 × 2 位数，最多 4 位

所以我们可以先开一个长度为 `m + n` 的数组，专门存每一位的结果。

## 解法：结果数组模拟乘法（推荐）

### 思路

假设：

- `num1[i]` 表示第一个字符串中的某一位
- `num2[j]` 表示第二个字符串中的某一位

那么它们相乘后，会对结果数组中的两个位置产生影响：

```text
p1 = i + j
p2 = i + j + 1
```

这里的含义可以理解成：

- `p2` 放当前位
- `p1` 放进位

具体过程是：

1. 从后往前枚举 `num1` 的每一位
2. 从后往前枚举 `num2` 的每一位
3. 计算两位数字乘积
4. 加到结果数组当前对应的位置上
5. 把个位留在 `p2`
6. 把十位进位加到 `p1`

最后再把结果数组转成字符串，并去掉前导零。

### 为什么位置是 `i + j` 和 `i + j + 1`

这是这题最关键的地方。

如果两个数字的某两位相乘，它们所在的结果位置，和它们离末尾有多远有关。

例如：

```text
num1 = "123"
num2 = "456"
```

`3 * 6` 是最低位相乘，它应该落在结果的最右边附近。

而更高位的乘积会更靠左。

统一到代码里，最方便的写法就是：

```text
p1 = i + j
p2 = i + j + 1
```

这样就能自然处理所有位对齐问题。

### 以 `"123" * "456"` 为例

先建立结果数组：

```text
result = [0, 0, 0, 0, 0, 0]
```

长度是 `3 + 3 = 6`。

然后从后往前枚举：

- `3 * 6 = 18`
- 把 `8` 放到当前位置
- 把 `1` 作为进位加到前一位

后面继续累加其它位的乘积，最终会得到：

```text
56088
```

### 特殊情况：有一个数是 `"0"`

如果：

```text
num1 == "0" 或 num2 == "0"
```

那结果一定就是：

```text
"0"
```

这个可以提前返回。

### 复杂度分析

- **时间复杂度：** `O(m * n)`，其中 `m` 和 `n` 分别是两个字符串长度
- **空间复杂度：** `O(m + n)`，用于存结果数组

## 代码

### C++

```cpp
#include <string>
#include <vector>
using namespace std;

class Solution {
public:
    string multiply(string num1, string num2) {
        if (num1 == "0" || num2 == "0") {
            return "0";
        }

        int m = num1.size(), n = num2.size();
        vector<int> result(m + n, 0);

        for (int i = m - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                int mul = (num1[i] - '0') * (num2[j] - '0');
                int p1 = i + j;
                int p2 = i + j + 1;

                int sum = mul + result[p2];
                result[p2] = sum % 10;
                result[p1] += sum / 10;
            }
        }

        string ans;
        for (int num : result) {
            if (!(ans.empty() && num == 0)) {
                ans.push_back(num + '0');
            }
        }

        return ans;
    }
};
```

### Python

```python
class Solution:
    def multiply(self, num1: str, num2: str) -> str:
        if num1 == "0" or num2 == "0":
            return "0"

        m, n = len(num1), len(num2)
        result = [0] * (m + n)

        for i in range(m - 1, -1, -1):
            for j in range(n - 1, -1, -1):
                mul = int(num1[i]) * int(num2[j])
                p1 = i + j
                p2 = i + j + 1

                total = mul + result[p2]
                result[p2] = total % 10
                result[p1] += total // 10

        ans = []
        for num in result:
            if not (not ans and num == 0):
                ans.append(str(num))

        return "".join(ans)
```
