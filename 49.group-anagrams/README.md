# 49. 字母异位词分组 (Group Anagrams)

## 题目描述

给你一个字符串数组 `strs`，请你将字母异位词组合在一起。你可以按任意顺序返回结果列表。

字母异位词指：

- 两个字符串包含的字母完全相同
- 只是字母顺序不同

**示例 1：**

```text
输入：strs = ["eat","tea","tan","ate","nat","bat"]
输出：[["bat"],["nat","tan"],["ate","eat","tea"]]
```

**示例 2：**

```text
输入：strs = [""]
输出：[[""]]
```

**示例 3：**

```text
输入：strs = ["a"]
输出：[["a"]]
```

**提示：**

- `1 <= strs.length <= 10^4`
- `0 <= strs[i].length <= 100`
- `strs[i]` 仅包含小写字母

## 算法知识

### 哈希表分组

这题的核心是：

- 怎么判断两个字符串是不是字母异位词
- 怎么把属于同一类的字符串放进同一组

最自然的方法是用哈希表：

- `key`：表示一类异位词的共同特征
- `value`：属于这一类的所有字符串

这样遍历每个字符串时，只要算出它的 `key`，就能直接放进对应的组里。

### 排序后的字符串作为 key

如果两个字符串互为字母异位词，那么它们排序后一定相同。

例如：

```text
"eat" -> "aet"
"tea" -> "aet"
"ate" -> "aet"
```

所以：

- 排序后的字符串可以作为这一组异位词的“统一标识”

这就是这题最经典的做法。

## 解法：排序后作为哈希 key（推荐）

### 思路

我们遍历数组中的每个字符串 `s`：

1. 复制一份 `s`
2. 对复制后的字符串排序
3. 排序结果作为哈希表的 key
4. 把原字符串 `s` 加入这个 key 对应的数组中

最后把哈希表里的所有分组取出来即可。

### 为什么这样做是对的

因为异位词的本质是：

- 字符种类相同
- 每种字符出现次数相同

而把字符串排序以后：

- 相同字符会排在一起
- 所有字母和次数的信息都会被固定下来

所以只要两个字符串排序后一样，它们就一定属于同一组异位词。

反过来，如果排序后不一样，那它们一定不是异位词。

### 以示例一为例

```text
["eat","tea","tan","ate","nat","bat"]
```

遍历时：

- `"eat"` 排序后是 `"aet"`，放进 `"aet"` 组
- `"tea"` 排序后也是 `"aet"`，放进同一组
- `"tan"` 排序后是 `"ant"`，放进 `"ant"` 组
- `"ate"` 排序后还是 `"aet"`，继续放进 `"aet"` 组
- `"nat"` 排序后是 `"ant"`，放进 `"ant"` 组
- `"bat"` 排序后是 `"abt"`，放进 `"abt"` 组

最后得到：

```text
"aet" -> ["eat","tea","ate"]
"ant" -> ["tan","nat"]
"abt" -> ["bat"]
```

### 复杂度分析

设：

- 字符串个数为 `n`
- 每个字符串平均长度为 `k`

那么：

- **时间复杂度：** `O(n * k log k)`，因为每个字符串都要排序
- **空间复杂度：** `O(n * k)`，用于哈希表存储分组结果

## 代码

### C++

```cpp
#include <vector>
#include <string>
#include <unordered_map>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> groups;

        for (string s : strs) {
            string key = s;
            sort(key.begin(), key.end());
            groups[key].push_back(s);
        }

        vector<vector<string>> ans;
        for (auto& [key, group] : groups) {
            ans.push_back(group);
        }

        return ans;
    }
};
```

### Python

```python
from collections import defaultdict
from typing import List


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        groups = defaultdict(list)

        for s in strs:
            key = "".join(sorted(s))
            groups[key].append(s)

        return list(groups.values())
```
