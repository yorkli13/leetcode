# 60. 排列序列 (Permutation Sequence)

## 题目描述

给出集合 `[1,2,3,...,n]`，其所有元素共有 `n!` 种排列。
按字典序列出所有排列后，返回第 `k` 个排列。

**示例 1：**

```text
输入：n = 3, k = 3
输出："213"
```

**示例 2：**

```text
输入：n = 4, k = 9
输出："2314"
```

**示例 3：**

```text
输入：n = 3, k = 1
输出："123"
```

**提示：**

- `1 <= n <= 9`
- `1 <= k <= n!`

## 算法知识

### 阶乘分组

这题最关键的知识点，不是生成所有排列，而是理解：

> 按字典序排列时，所有排列会按“第一位是什么”被均匀分成很多组。

以 `n = 4` 为例：

- 第一位是 `1` 的排列有 `3! = 6` 个
- 第一位是 `2` 的排列也有 `3! = 6` 个
- 第一位是 `3` 的排列也有 `3! = 6` 个
- 第一位是 `4` 的排列也有 `3! = 6` 个

因为第一位一旦确定，后面还剩下 `n - 1` 个数可以任意排列，所以每组大小就是：

```text
(n - 1)!
```

这就说明，我们可以不用真的把所有排列都列出来，而是直接根据 `k` 落在哪一组里，确定当前这一位应该选谁。

## 解法：用阶乘定位每一位数字（推荐）

### 思路

先准备两个东西：

1. 一个数字池 `nums = [1,2,3,...,n]`
2. 一个阶乘数组，用来快速知道每一层每组有多大

然后从高位到低位，逐位确定答案。

### 为什么要先把 `k` 变成从 0 开始

题目里的 `k` 是“第 1 个、第 2 个、第 3 个……”

但在代码里做分组时，更适合用从 0 开始的编号：

```text
k = k - 1
```

这样后面求：

```text
index = k / groupSize
```

就能直接得到当前这一位应该选数字池中的第几个元素。

### 每一位怎么选

假设当前还剩 `m` 个数字没用，那么：

- 每一个候选数字对应一整组排列
- 每组大小都是 `(m - 1)!`

于是：

```text
index = k / (m - 1)!
```

表示当前这一位要从剩余数字里选第 `index` 个。

选完以后，把这个数字从数字池中删掉，然后更新：

```text
k = k % (m - 1)!
```

因为接下来我们只需要在当前这一组内部继续找下一位。

### 以 `n = 4, k = 9` 为例

全部数字池开始是：

```text
[1,2,3,4]
```

先转成从 0 开始：

```text
k = 8
```

#### 第 1 位

还剩 4 个数，每组大小是：

```text
3! = 6
```

所以：

```text
index = 8 / 6 = 1
```

说明第 1 位选数字池中下标 1 的数，也就是 `2`。

答案变成：

```text
"2"
```

数字池变成：

```text
[1,3,4]
```

更新：

```text
k = 8 % 6 = 2
```

#### 第 2 位

还剩 3 个数，每组大小是：

```text
2! = 2
```

所以：

```text
index = 2 / 2 = 1
```

选数字池中下标 1 的数，也就是 `3`。

答案变成：

```text
"23"
```

数字池变成：

```text
[1,4]
```

更新：

```text
k = 2 % 2 = 0
```

#### 第 3 位

还剩 2 个数，每组大小是：

```text
1! = 1
```

所以：

```text
index = 0 / 1 = 0
```

选 `1`，答案变成 `"231"`。

最后剩下 `4`，得到：

```text
"2314"
```

### 为什么这种方法比生成所有排列更优

如果先生成所有排列，再取第 `k` 个：

- 会做很多完全没必要的工作
- 时间和空间都更大

而这里我们每次只关心：

- 当前这一位该选谁

所以本质上是在“直接构造答案”，没有枚举所有排列。

### 复杂度分析

- **时间复杂度：** `O(n^2)`，因为一共选 `n` 次，每次从数组中删除一个元素可能需要 `O(n)`
- **空间复杂度：** `O(n)`，用于保存数字池和阶乘数组

## 代码

### C++

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    string getPermutation(int n, int k) {
        vector<int> factorial(n + 1, 1);
        for (int i = 1; i <= n; i++) {
            factorial[i] = factorial[i - 1] * i;
        }

        vector<int> nums;
        for (int i = 1; i <= n; i++) {
            nums.push_back(i);
        }

        k--;
        string ans;

        for (int i = n; i >= 1; i--) {
            int groupSize = factorial[i - 1];
            int index = k / groupSize;

            ans += to_string(nums[index]);
            nums.erase(nums.begin() + index);

            k %= groupSize;
        }

        return ans;
    }
};
```

### Python

```python
class Solution:
    def getPermutation(self, n: int, k: int) -> str:
        factorial = [1] * (n + 1)
        for i in range(1, n + 1):
            factorial[i] = factorial[i - 1] * i

        nums = [str(i) for i in range(1, n + 1)]
        k -= 1
        ans = []

        for i in range(n, 0, -1):
            group_size = factorial[i - 1]
            index = k // group_size

            ans.append(nums.pop(index))
            k %= group_size

        return "".join(ans)
```
