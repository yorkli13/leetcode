# 69. x 的平方根 (Sqrt(x))

## 题目描述

给你一个非负整数 `x`，计算并返回 `x` 的算术平方根。

由于返回类型是整数，结果只保留整数部分，小数部分将被舍去。

注意：不允许使用任何内置指数函数和算符，例如 `pow(x, 0.5)` 或 `x ** 0.5`。

**示例 1：**

```text
输入：x = 4
输出：2
```

**示例 2：**

```text
输入：x = 8
输出：2
解释：8 的算术平方根是 2.82842...，由于返回类型是整数，小数部分将被舍去
```

**提示：**

- `0 <= x <= 2^31 - 1`

## 算法知识

### 二分查找

这题虽然看起来是数学题，但它很适合用二分查找。

因为我们要找的是一个整数 `k`，满足：

```text
k^2 <= x
```

并且这个 `k` 要尽可能大。

随着 `k` 变大，`k^2` 也会单调变大，所以这就是一个标准的“在有序答案空间中找边界”的问题。

## 解法：二分查找平方根的整数部分（推荐）

### 思路

我们在区间：

```text
[0, x]
```

里二分查找答案。

每次取中点：

```text
mid
```

然后看：

```text
mid * mid
```

和 `x` 的关系。

### 三种情况

#### 1. 如果 `mid * mid == x`

说明刚好找到了平方根，直接返回 `mid`。

#### 2. 如果 `mid * mid < x`

说明 `mid` 还不够大，它有可能是答案，也有可能真正答案在右边。

所以这时：

- 先把 `mid` 记成一个当前可行答案
- 再继续去右边找更大的

#### 3. 如果 `mid * mid > x`

说明 `mid` 太大了，答案一定在左边。

所以缩小右边界。

### 为什么要记录一个 `ans`

因为这题不一定存在整数平方根。

例如：

```text
x = 8
```

真正平方根是：

```text
2.828...
```

但题目要返回整数部分，也就是：

```text
2
```

所以我们真正想找的是：

> 最大的那个满足 `k^2 <= x` 的整数

这类题通常就要在二分过程中记录“当前最后一个合法值”。

### 为什么要用 `long long`

因为 `x` 最大可以到：

```text
2^31 - 1
```

如果直接用 `int` 去算：

```cpp
mid * mid
```

可能会溢出。

例如 `mid` 接近 50000 时，平方就已经很大了。

所以代码里通常写：

```cpp
long long square = 1LL * mid * mid;
```

这样乘法会在 `long long` 范围里进行，更安全。

### 以 `x = 8` 为例

开始时搜索范围：

```text
left = 0, right = 8
```

1. `mid = 4`
   - `4^2 = 16 > 8`
   - 去左边
2. `mid = 1`
   - `1^2 = 1 < 8`
   - 记录答案为 1，去右边
3. `mid = 2`
   - `2^2 = 4 < 8`
   - 记录答案为 2，去右边
4. `mid = 3`
   - `3^2 = 9 > 8`
   - 去左边

最后二分结束，记录下来的最大合法值是：

```text
2
```

所以答案就是 2。

### 复杂度分析

- **时间复杂度：** `O(log x)`，每次把搜索区间缩小一半
- **空间复杂度：** `O(1)`，只使用常数个变量

## 代码

### C++

```cpp
class Solution {
public:
    int mySqrt(int x) {
        int left = 0, right = x;
        int ans = 0;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            long long square = 1LL * mid * mid;

            if (square == x) {
                return mid;
            } else if (square < x) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        return ans;
    }
};
```

### Python

```python
class Solution:
    def mySqrt(self, x: int) -> int:
        left, right = 0, x
        ans = 0

        while left <= right:
            mid = left + (right - left) // 2
            square = mid * mid

            if square == x:
                return mid
            elif square < x:
                ans = mid
                left = mid + 1
            else:
                right = mid - 1

        return ans
```
