# 14. 最长公共前缀 (Longest Common Prefix)

## 题目描述

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 `""`。

**示例 1：**

```text
输入：strs = ["flower","flow","flight"]
输出："fl"
```

**示例 2：**

```text
输入：strs = ["dog","racecar","car"]
输出：""
解释：输入不存在公共前缀。
```

**提示：**

- `1 <= strs.length <= 200`
- `0 <= strs[i].length <= 200`
- `strs[i]` 仅由小写英文字母组成

## 算法知识

### 字符串前缀

一个字符串的前缀，是指从开头开始连续取出的若干个字符。

例如字符串 `"flower"` 的前缀可以是：

- `""`
- `"f"`
- `"fl"`
- `"flo"`
- `"flow"`
- `"flowe"`
- `"flower"`

本题要求的是多个字符串共同拥有的最长前缀。

### 横向扫描

横向扫描的思路是：

1. 先把第一个字符串当作当前公共前缀
2. 然后依次与后面的每个字符串比较
3. 每比较一次，就把公共前缀缩短到两者的共同部分
4. 最终剩下的就是所有字符串的最长公共前缀

这种方法实现简单，也很符合直觉。

## 解法：横向扫描（推荐）

### 思路

假设：

```text
strs = ["flower", "flow", "flight"]
```

一开始令：

```text
prefix = "flower"
```

然后依次处理后面的字符串：

1. `prefix` 和 `"flow"` 比较，得到公共前缀 `"flow"`
2. `"flow"` 和 `"flight"` 比较，得到公共前缀 `"fl"`

最终答案就是 `"fl"`。

具体实现时，可以这样做：

1. 令 `prefix = strs[0]`
2. 遍历后面的每个字符串 `strs[i]`
3. 当 `strs[i]` 不是以 `prefix` 开头时，就不断缩短 `prefix`
4. 如果 `prefix` 缩短为空字符串，说明没有公共前缀，直接返回 `""`

### 为什么这样做可行？

因为最长公共前缀一定不会比任意一个字符串更长。

每次把当前前缀和下一个字符串比较后，保留下来的前缀一定是“到目前为止所有字符串的公共前缀”。随着比较的字符串越来越多，前缀只会变短，不会变长。

因此，最后留下来的结果就是全局的最长公共前缀。

### 复杂度分析

- **时间复杂度**：O(S)，其中 `S` 是所有字符串字符总数。每个字符最多被比较常数次。
- **空间复杂度**：O(1)，除返回结果外只使用常数额外空间。

## 代码

### C++

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        string prefix = strs[0];

        for (int i = 1; i < strs.size(); i++) {
            while (strs[i].find(prefix) != 0) {
                prefix.pop_back();
                if (prefix.empty()) {
                    return "";
                }
            }
        }

        return prefix;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        prefix = strs[0]

        for i in range(1, len(strs)):
            while not strs[i].startswith(prefix):
                prefix = prefix[:-1]
                if not prefix:
                    return ""

        return prefix
```
