# 29. 两数相除 (Divide Two Integers)

## 题目描述

给你两个整数，被除数 `dividend` 和除数 `divisor`。要求在**不使用乘法、除法和取模运算符**的情况下，计算 `dividend / divisor` 的商。

整数除法应该向零截断，也就是直接舍去小数部分。例如：

- `8.345` 截断为 `8`
- `-2.7335` 截断为 `-2`

返回商，并且结果必须限制在 32 位有符号整数范围内：

```text
[-2^31, 2^31 - 1]
```

如果商严格大于 `2^31 - 1`，返回 `2^31 - 1`；
如果商严格小于 `-2^31`，返回 `-2^31`。

**示例 1：**

```text
输入：dividend = 10, divisor = 3
输出：3
解释：10 / 3 = 3.33333...，向零截断后为 3。
```

**示例 2：**

```text
输入：dividend = 7, divisor = -3
输出：-2
解释：7 / -3 = -2.33333...，向零截断后为 -2。
```

**提示：**

- `-2^31 <= dividend, divisor <= 2^31 - 1`
- `divisor != 0`

## 算法知识

### 减法模拟除法

最朴素的想法是：

- 每次从被除数里减去一个除数
- 看能减多少次

减的次数就是商。

但如果直接一下一下地减，时间复杂度会太高。例如：

```text
10^9 / 1
```

就需要减十亿次，显然会超时。

### 二进制拆分

为了避免一下一下地减，我们可以直接思考：

商的每一位二进制位是否能取到 `1`？

也就是说，我们尝试判断：

- `divisor * 2^31` 能不能从 `dividend` 中减掉
- `divisor * 2^30` 能不能减掉
- ...
- `divisor * 2^0` 能不能减掉

如果能减掉，就说明商的这一位应该是 `1`，同时把这一部分从被除数中扣掉。

这样从高位到低位扫一遍，就能直接构造出最终答案。

### 为什么要先转成更大范围的整数？

本题的边界值里最麻烦的是：

```text
INT_MIN = -2147483648
```

它的绝对值比 `INT_MAX` 大 1，所以如果还在 32 位 `int` 范围里直接取绝对值，可能会溢出。

因此实现时可以先把 `dividend` 和 `divisor` 转成更大范围的整数类型，再做绝对值和位移运算，这样逻辑会更直观，也更稳。

## 解法：位运算 + 二进制拆分（推荐）

### 思路

1. 先处理特殊情况：
   - 如果 `dividend == INT_MIN && divisor == -1`，结果会超过 `INT_MAX`，直接返回 `INT_MAX`
2. 判断结果符号是否为负
3. 把 `dividend` 和 `divisor` 转成更大范围整数，并取绝对值
4. 从高位到低位枚举 `shift`
5. 如果 `(divisor << shift)` 仍然不大于当前被除数，说明商中可以包含这一位
6. 就把这一位加入答案，并从被除数中减去对应值
7. 枚举结束后，根据符号返回结果

### 如何理解“从高位到低位枚举”？

假设：

```text
dividend = 100
divisor = 3
```

我们会尝试：

- `3 * 32 = 96` 能不能减掉？可以
- 那么商至少包含 `32`
- 减掉后剩余 `4`
- 再继续试更小的 2 的幂倍
- `3 * 1 = 3` 还可以减掉

于是最终商就是：

```text
32 + 1 = 33
```

这和二进制构造答案的过程完全一致。

### 为什么这样做可行？

因为任何整数都可以写成若干个 2 的幂之和。

从高位到低位检查，本质上就是在判断商的每一位二进制位是不是 `1`。一旦某一位可以取到，就立刻扣掉对应的值，继续判断后面的位。

这样只需要固定扫描 32 次左右，就能构造出最终答案。

### 复杂度分析

- **时间复杂度**：O(log n)，这里的 `log n` 本质上对应整数二进制位数；对 32 位整数来说，也可以看成常数级 32 次循环。
- **空间复杂度**：O(1)，只使用常数额外空间。

## 代码

### C++

```cpp
#include <climits>
#include <cstdlib>
using namespace std;

class Solution {
public:
    int divide(int dividend, int divisor) {
        if (dividend == INT_MIN && divisor == -1) {
            return INT_MAX;
        }

        bool negative = (dividend > 0) ^ (divisor > 0);

        long long a = llabs((long long)dividend);
        long long b = llabs((long long)divisor);

        long long ans = 0;
        for (int shift = 31; shift >= 0; shift--) {
            if ((b << shift) <= a) {
                a -= (b << shift);
                ans += (1LL << shift);
            }
        }

        return negative ? -(int)ans : (int)ans;
    }
};
```

### Python

```python
class Solution:
    def divide(self, dividend: int, divisor: int) -> int:
        INT_MIN, INT_MAX = -2**31, 2**31 - 1

        if dividend == INT_MIN and divisor == -1:
            return INT_MAX

        negative = (dividend > 0) ^ (divisor > 0)
        a = abs(dividend)
        b = abs(divisor)

        ans = 0
        for shift in range(31, -1, -1):
            if (b << shift) <= a:
                a -= (b << shift)
                ans += 1 << shift

        return -ans if negative else ans
```
