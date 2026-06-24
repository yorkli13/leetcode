# 7. 整数反转

## 题目描述

给你一个 32 位的有符号整数 `x`，返回将 `x` 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 `[−2^31, 2^31 − 1]`，就返回 0。

**假设环境不允许存储 64 位整数（有符号或无符号）。**

**示例 1：**

```
输入：x = 123
输出：321
```

**示例 2：**

```
输入：x = -123
输出：-321
```

**示例 3：**

```
输入：x = 120
输出：21
```

**示例 4：**

```
输入：x = 0
输出：0
```

**提示：**

- `-2^31 <= x <= 2^31 - 1`

## 算法知识

### 数学逐位取反法

核心思想：不断取出 `x` 的末位数字，拼接到结果 `ans` 尾部。

- 取末位：`digit = x % 10`（C++ 中负数取余结果也为负，符合需求）
- 去掉末位：`x /= 10`
- 拼接：`ans = ans * 10 + digit`
- 重复直到 `x == 0`

#### 溢出判断

题目**不允许使用 64 位整数**，所以不能在算完 `ans * 10 + digit` 后再用 `long long` 判断。需要在溢出发生**之前**检测：

- 正数溢出：`ans > INT_MAX / 10`，或者 `ans == INT_MAX / 10 && digit > 7`
- 负数溢出：`ans < INT_MIN / 10`，或者 `ans == INT_MIN / 10 && digit < -8`

（`INT_MAX = 2147483647`，末位为 7；`INT_MIN = -2147483648`，末位为 -8）

### 时间复杂度

- 时间复杂度：O(log|x|)，即数字的位数
- 空间复杂度：O(1)

## 解法

### 数学法（推荐）

循环取末位拼接到结果，每次迭代前检查是否即将溢出。

## 代码

### C++

```cpp
class Solution {
public:
    int reverse(int x) {
        int ans = 0;
        while (x != 0) {
            int digit = x % 10;
            x /= 10;

            // 正数溢出
            if (ans > INT_MAX / 10 || (ans == INT_MAX / 10 && digit > 7)) {
                return 0;
            }
            // 负数溢出
            if (ans < INT_MIN / 10 || (ans == INT_MIN / 10 && digit < -8)) {
                return 0;
            }

            ans = ans * 10 + digit;
        }
        return ans;
    }
};
```

### Python

```python
class Solution:
    def reverse(self, x: int) -> int:
        INT_MIN, INT_MAX = -2**31, 2**31 - 1

        ans = 0
        while x != 0:
            digit = x % 10  # Python 取余结果非负，需要处理符号
            x //= 10

            if x < 0:
                # Python 中负数 // 10 是向下取整，digit 会偏大
                # 修正：如果 x 是负数且 digit > 0，说明取余结果需要减 10
                if digit > 0:
                    digit -= 10
                    x += 1

            # 溢出判断
            if ans > INT_MAX // 10 or (ans == INT_MAX // 10 and digit > 7):
                return 0
            if ans < INT_MIN // 10 or (ans == INT_MIN // 10 and digit < -8):
                return 0

            ans = ans * 10 + digit

        return ans
```
