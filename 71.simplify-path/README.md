# 71. 简化路径 (Simplify Path)

## 题目描述

给你一个字符串 `path`，表示 Unix 风格的绝对路径，请你将它转化为更加简洁的规范路径。

在 Unix 风格路径中：

- `.` 表示当前目录
- `..` 表示上一级目录
- 多个连续的 `/` 等价于一个 `/`
- 规范路径必须以 `/` 开头
- 规范路径中不能包含多余的 `/`
- 规范路径中不能以 `/` 结尾，除非路径就是根目录 `/`

**示例 1：**

```text
输入：path = "/home/"
输出："/home"
解释：末尾的斜杠需要去掉
```

**示例 2：**

```text
输入：path = "/../"
输出："/"
解释：从根目录无法继续向上，所以仍然是根目录
```

**示例 3：**

```text
输入：path = "/home//foo/"
输出："/home/foo"
解释：多个连续的斜杠需要替换成一个斜杠
```

**提示：**

- `1 <= path.length <= 3000`
- `path` 由英文字母、数字、点 `'.'`、斜杠 `'/'` 或下划线 `'_'` 组成
- `path` 是一个有效的 Unix 风格绝对路径

## 算法知识

### 栈

这题很适合用栈来理解。

路径从左到右处理时，每遇到一个目录名，就表示进入了一个新目录，可以把它放入栈中。

如果遇到：

```text
..
```

就表示回到上一级目录，也就是把栈顶目录弹出。

如果遇到：

```text
.
```

表示当前目录，不需要做任何事。

这样处理完所有路径片段后，栈里剩下的目录名，就是最终规范路径中应该保留的目录层级。

## 解法：按 `/` 分割路径，用栈维护有效目录（推荐）

### 思路

我们把路径按照 `/` 分成一个个片段。

例如：

```text
"/home//foo/"
```

按 `/` 分割后，可以理解成这些片段：

```text
"home", "", "foo"
```

其中空字符串来自连续斜杠或者开头、结尾的斜杠，需要忽略。

### 每种片段怎么处理

对于每个路径片段 `part`：

#### 1. 如果 `part` 是空字符串

说明遇到了多余的 `/`，直接跳过。

例如：

```text
"/home//foo"
```

中间的 `//` 会产生空片段，但它不代表真实目录。

#### 2. 如果 `part` 是 `.`

表示当前目录，不会改变路径，也直接跳过。

#### 3. 如果 `part` 是 `..`

表示回到上一级目录。

如果栈不为空，就弹出栈顶目录。

如果栈已经为空，说明当前已经在根目录，不能继续向上，什么都不用做。

#### 4. 其他情况

说明这是一个普通目录名，把它加入栈。

### 最后怎么拼回路径

处理完所有片段后，栈里保存的就是从根目录到目标目录的有效目录名。

如果栈为空，说明结果就是根目录：

```text
/
```

否则按顺序拼接：

```text
 "/" + dir1 + "/" + dir2 + ...
```

### 以 `"/a/./b/../../c/"` 为例

路径片段依次是：

```text
a, ., b, .., .., c
```

处理过程：

1. `a` 是普通目录，入栈：`[a]`
2. `.` 表示当前目录，跳过：`[a]`
3. `b` 是普通目录，入栈：`[a, b]`
4. `..` 回到上一级，弹出 `b`：`[a]`
5. `..` 回到上一级，弹出 `a`：`[]`
6. `c` 是普通目录，入栈：`[c]`

最后拼成：

```text
/c
```

### 为什么用栈合适

路径中的 `..` 总是影响“最近进入的那个目录”。

这正好符合栈的特点：

- 后进入的目录
- 会先被 `..` 取消掉

也就是“后进先出”。

### 复杂度分析

- **时间复杂度：** `O(n)`，其中 `n` 是路径字符串长度
- **空间复杂度：** `O(n)`，用于保存路径片段和结果

## 代码

### C++

```cpp
#include <string>
#include <vector>
using namespace std;

class Solution {
public:
    string simplifyPath(string path) {
        vector<string> st;
        int n = path.size();
        int i = 0;

        while (i < n) {
            while (i < n && path[i] == '/') {
                i++;
            }

            string part;
            while (i < n && path[i] != '/') {
                part.push_back(path[i]);
                i++;
            }

            if (part.empty() || part == ".") {
                continue;
            } else if (part == "..") {
                if (!st.empty()) {
                    st.pop_back();
                }
            } else {
                st.push_back(part);
            }
        }

        if (st.empty()) {
            return "/";
        }

        string ans;
        for (const string& dir : st) {
            ans += "/";
            ans += dir;
        }

        return ans;
    }
};
```

### Python

```python
class Solution:
    def simplifyPath(self, path: str) -> str:
        stack = []

        for part in path.split("/"):
            if part == "" or part == ".":
                continue
            elif part == "..":
                if stack:
                    stack.pop()
            else:
                stack.append(part)

        return "/" + "/".join(stack)
```
