# 68. 文本左右对齐 (Text Justification)

## 题目描述

给定一个单词数组 `words` 和一个长度 `maxWidth`，重新排版单词，使每行恰好有 `maxWidth` 个字符，且左右两端对齐。

你应该使用“贪心”方式来放置单词，也就是说：

- 每一行尽可能多地放单词
- 必要时用空格填充，使该行长度达到 `maxWidth`

额外规则：

1. 单词之间至少有一个空格
2. 如果一行需要插入额外空格，空格尽量平均分配
3. 如果不能平均分配，左边的空隙比右边的空隙多放一个空格
4. 最后一行左对齐，单词之间只放一个空格，末尾补空格

**示例 1：**

```text
输入：words = ["This", "is", "an", "example", "of", "text", "justification."], maxWidth = 16
输出：
[
   "This    is    an",
   "example  of text",
   "justification.  "
]
```

**示例 2：**

```text
输入：words = ["What","must","be","acknowledgment","shall","be"], maxWidth = 16
输出：
[
  "What   must   be",
  "acknowledgment  ",
  "shall be        "
]
```

**提示：**

- `1 <= words.length <= 300`
- `1 <= words[i].length <= 20`
- `words[i]` 仅由英文字母和符号组成
- `1 <= maxWidth <= 100`
- `words[i].length <= maxWidth`

## 算法知识

### 贪心 + 字符串模拟

这题的核心不是什么高深数据结构，而是把排版规则拆成两个阶段：

1. 先决定这一行放哪些单词
2. 再决定这些单词之间的空格怎么分

为什么先用贪心？

因为题目明确要求：

- 每一行尽可能多放单词

所以当前行能放多少就放多少，不需要回头修改，这正是贪心策略。

## 解法：贪心确定每一行，再按规则分配空格（推荐）

### 思路总览

我们用一个指针从左到右扫描 `words`。

对于每一行：

1. 先贪心地找出这一行能放到哪里
2. 计算这一行所有单词总长度
3. 判断这一行是不是最后一行，或者是不是只有一个单词
4. 按对应规则拼出这一整行字符串

### 第一步：确定这一行有哪些单词

假设当前从 `words[left]` 开始放。

我们不断尝试加入下一个单词，只要加入后这一行总长度不超过 `maxWidth`，就继续放。

这里长度要包括：

- 单词本身长度
- 单词之间至少 1 个空格

所以这是一个很标准的“贪心装满当前行”过程。

### 第二步：分情况处理这一行

一行确定好单词后，有两种大情况。

#### 情况 1：最后一行，或者这一行只有一个单词

这两种都按左对齐处理：

1. 单词之间只放一个空格
2. 行末补空格，补到 `maxWidth`

比如：

```text
"shall be        "
```

#### 情况 2：普通中间行

这时要左右对齐。

设：

- 这一行单词总长度是 `wordLen`
- 这一行有 `gaps` 个空隙

那么总共要分配的空格数是：

```text
spaces = maxWidth - wordLen
```

接下来要把这些空格尽量平均分到每个空隙里。

### 第三步：空格怎么平均分配

如果总空格数是 `spaces`，空隙数是 `gaps`，那么：

```text
base = spaces / gaps
extra = spaces % gaps
```

含义是：

- 每个空隙至少分到 `base` 个空格
- 前 `extra` 个空隙再多分 1 个空格

这就正好满足题目要求：

- 尽量平均
- 左边比右边多

### 以示例 1 第一行为例

```text
words = ["This", "is", "an", ...]
maxWidth = 16
```

这一行能放：

```text
"This", "is", "an"
```

单词总长度：

```text
4 + 2 + 2 = 8
```

有两个空隙：

```text
gaps = 2
```

总共需要补的空格数：

```text
16 - 8 = 8
```

平均分：

```text
base = 8 / 2 = 4
extra = 8 % 2 = 0
```

所以两个空隙各放 4 个空格，得到：

```text
"This    is    an"
```

### 为什么这题适合拆成“选单词 + 分空格”

因为一行一旦确定了放哪些单词，后面的事情就只剩格式化。

也就是说，这题其实是两个独立子任务：

1. 行内容选择
2. 行字符串构造

把它们拆开后，代码会清楚很多，不容易混乱。

### 复杂度分析

- **时间复杂度：** `O(n * L)`，其中 `n` 是单词数，`L` 是字符串拼接相关开销；整体可以认为是线性处理所有字符
- **空间复杂度：** `O(ans)`，用于保存输出结果

## 代码

### C++

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    vector<string> fullJustify(vector<string>& words, int maxWidth) {
        vector<string> ans;
        int n = words.size();
        int left = 0;

        while (left < n) {
            int right = left;
            int wordLen = 0;

            while (right < n &&
                   wordLen + words[right].size() + (right - left) <= maxWidth) {
                wordLen += words[right].size();
                right++;
            }

            int numWords = right - left;
            int gaps = numWords - 1;
            string line;

            if (right == n || gaps == 0) {
                for (int i = left; i < right; i++) {
                    if (i > left) {
                        line += ' ';
                    }
                    line += words[i];
                }
                line += string(maxWidth - line.size(), ' ');
            } else {
                int spaces = maxWidth - wordLen;
                int base = spaces / gaps;
                int extra = spaces % gaps;

                for (int i = left; i < right; i++) {
                    line += words[i];
                    if (i < right - 1) {
                        int currSpaces = base + (i - left < extra ? 1 : 0);
                        line += string(currSpaces, ' ');
                    }
                }
            }

            ans.push_back(line);
            left = right;
        }

        return ans;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def fullJustify(self, words: List[str], maxWidth: int) -> List[str]:
        ans = []
        n = len(words)
        left = 0

        while left < n:
            right = left
            word_len = 0

            while right < n and word_len + len(words[right]) + (right - left) <= maxWidth:
                word_len += len(words[right])
                right += 1

            num_words = right - left
            gaps = num_words - 1

            if right == n or gaps == 0:
                line = " ".join(words[left:right])
                line += " " * (maxWidth - len(line))
            else:
                spaces = maxWidth - word_len
                base = spaces // gaps
                extra = spaces % gaps

                parts = []
                for i in range(left, right):
                    parts.append(words[i])
                    if i < right - 1:
                        curr_spaces = base + (1 if i - left < extra else 0)
                        parts.append(" " * curr_spaces)
                line = "".join(parts)

            ans.append(line)
            left = right

        return ans
```
