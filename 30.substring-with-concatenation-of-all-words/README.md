# 30. 串联所有单词的子串 (Substring with Concatenation of All Words)

## 题目描述

给定一个字符串 `s` 和一个字符串数组 `words`。`words` 中所有字符串长度相同。

`s` 中的一个串联子串，是指一个包含 `words` 中所有字符串以任意顺序排列连接起来的子串。

例如，如果：

```text
words = ["ab", "cd", "ef"]
```

那么 `"abcdef"`、`"abefcd"`、`"efcdab"`、`"cdabef"` 都是串联子串；
但 `"acdbef"` 不是，因为它不是由 `words` 中的整个单词拼接而成。

请你返回 `s` 中所有串联子串的开始索引。你可以以任意顺序返回答案。

**示例 1：**

```text
输入：s = "barfoothefoobarman", words = ["foo","bar"]
输出：[0,9]
解释：
从下标 0 开始的子串是 "barfoo"，它是 ["bar","foo"] 的一个排列。
从下标 9 开始的子串是 "foobar"，它是 ["foo","bar"] 的一个排列。
```

**示例 2：**

```text
输入：s = "wordgoodgoodgoodbestword", words = ["word","good","best","word"]
输出：[]
```

**示例 3：**

```text
输入：s = "barfoofoobarthefoobarman", words = ["bar","foo","the"]
输出：[6,9,12]
```

**提示：**

- `1 <= s.length <= 10^4`
- `1 <= words.length <= 5000`
- `1 <= words[i].length <= 30`
- `words[i]` 和 `s` 由小写英文字母组成

## 算法知识

### 哈希表计数

本题需要判断一个窗口中的单词出现次数，是否和 `words` 中要求的次数完全一致。

因此非常适合用哈希表来记录：

- 每个单词应该出现多少次
- 当前窗口中每个单词实际出现了多少次

### 滑动窗口

普通的逐字符滑动窗口在这里不够自然，因为本题的匹配单位不是单个字符，而是“长度固定的单词”。

由于 `words` 中所有单词长度都相同，设这个长度为 `wordLen`，那么我们可以把字符串 `s` 按照 `wordLen` 为步长进行分组扫描。

例如，如果 `wordLen = 3`，那么起点只需要考虑：

- 从下标 `0` 开始的分组
- 从下标 `1` 开始的分组
- 从下标 `2` 开始的分组

每一组里都按 `3` 个字符为一个单词向后滑动。

## 解法：分组滑动窗口 + 哈希表（推荐）

### 思路

设：

- `wordLen = words[0].length()`
- `wordCount = words.size()`
- `windowLen = wordLen * wordCount`

先用哈希表 `need` 统计 `words` 中每个单词需要出现的次数。

然后对每个起始偏移量 `offset`（从 `0` 到 `wordLen - 1`）分别做滑动窗口：

1. 维护窗口左右边界 `left`、`right`
2. 每次从 `right` 位置取一个长度为 `wordLen` 的单词
3. 如果这个单词不在 `need` 中：
   - 当前窗口直接失效
   - 清空窗口计数，重新从下一个位置开始
4. 如果这个单词在 `need` 中：
   - 加入当前窗口计数
   - 如果某个单词出现次数超了，就不断右移 `left` 缩小窗口
5. 当窗口中恰好包含 `wordCount` 个单词时：
   - 说明找到一个合法答案
   - 记录 `left`

### 为什么要按 `wordLen` 分组？

因为每个单词长度固定，所以合法子串一定是由若干个整块单词拼接起来的。

如果不按单词长度对齐，而是逐字符随便滑动，就会把很多不可能成为单词边界的位置也算进去，处理会变得混乱。

按 `0 ~ wordLen-1` 分组后，每一组都能保证取出来的都是“可能的单词块”，逻辑会清晰很多。

### 为什么这样做可行？

对于任意一个合法答案，它的起点对 `wordLen` 取模后，一定属于某一组偏移量。

在那一组里，我们按单词块移动窗口，并用哈希表精确维护单词出现次数：

- 缺词不行
- 多词不行
- 不在 `words` 里的单词也不行

因此只要窗口中单词数和出现次数都刚好匹配，就可以确定这是一个合法起点。

### 复杂度分析

- **时间复杂度**：O(n * wordLen) 更准确地说是 O(n)，因为每个字符位置在所属分组中最多被窗口左右边界访问常数次。
- **空间复杂度**：O(m)，其中 `m` 是 `words` 中不同单词的个数，用于哈希表计数。

## 代码

### C++

```cpp
#include <vector>
#include <string>
#include <unordered_map>
using namespace std;

class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        vector<int> ans;
        if (s.empty() || words.empty()) {
            return ans;
        }

        int wordLen = words[0].size();
        int wordCount = words.size();
        unordered_map<string, int> need;
        for (const string& word : words) {
            need[word]++;
        }

        for (int offset = 0; offset < wordLen; offset++) {
            int left = offset, count = 0;
            unordered_map<string, int> window;

            for (int right = offset; right + wordLen <= s.size(); right += wordLen) {
                string word = s.substr(right, wordLen);

                if (!need.count(word)) {
                    window.clear();
                    count = 0;
                    left = right + wordLen;
                    continue;
                }

                window[word]++;
                count++;

                while (window[word] > need[word]) {
                    string leftWord = s.substr(left, wordLen);
                    window[leftWord]--;
                    count--;
                    left += wordLen;
                }

                if (count == wordCount) {
                    ans.push_back(left);
                }
            }
        }

        return ans;
    }
};
```

### Python

```python
from typing import List
from collections import Counter, defaultdict


class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        if not s or not words:
            return []

        word_len = len(words[0])
        word_count = len(words)
        need = Counter(words)
        ans = []

        for offset in range(word_len):
            left = offset
            count = 0
            window = defaultdict(int)

            for right in range(offset, len(s) - word_len + 1, word_len):
                word = s[right:right + word_len]

                if word not in need:
                    window.clear()
                    count = 0
                    left = right + word_len
                    continue

                window[word] += 1
                count += 1

                while window[word] > need[word]:
                    left_word = s[left:left + word_len]
                    window[left_word] -= 1
                    count -= 1
                    left += word_len

                if count == word_count:
                    ans.append(left)

        return ans
```
