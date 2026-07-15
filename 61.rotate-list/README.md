# 61. 旋转链表 (Rotate List)

## 题目描述

给你一个链表的头节点 `head`，将链表每个节点向右移动 `k` 个位置。

**示例 1：**

```text
输入：head = [1,2,3,4,5], k = 2
输出：[4,5,1,2,3]
```

**示例 2：**

```text
输入：head = [0,1,2], k = 4
输出：[2,0,1]
```

**提示：**

- 链表中节点的数目在范围 `[0, 500]` 内
- `-100 <= Node.val <= 100`
- `0 <= k <= 2 * 10^9`

## 算法知识

### 链表成环

这题如果直接做“右移一次”然后重复 `k` 次，会非常慢。

因为每次右移都可能要走到链表尾部，复杂度会很高。

这题更自然的思路是把链表看成一个环：

1. 先找到链表长度 `n`
2. 把尾节点连回头节点，形成一个环
3. 再找到新的尾节点位置
4. 把环在那个位置断开

这样就能一次完成旋转。

## 解法：先连成环，再找到新头新尾（推荐）

### 思路

先处理几个特殊情况：

- 如果链表为空，直接返回
- 如果链表只有一个节点，怎么旋转都一样
- 如果 `k = 0`，也不用动

然后开始正式处理。

### 第一步：求链表长度，并找到尾节点

我们先从头遍历一遍链表，得到：

- 链表长度 `n`
- 最后一个节点 `tail`

### 第二步：把 `k` 化简

因为链表一共只有 `n` 个节点，所以每旋转 `n` 次，链表都会回到原样。

所以真正有效的移动次数是：

```text
k % n
```

如果：

```text
k % n == 0
```

说明转了一整圈，结果和原链表一样，直接返回即可。

### 第三步：把链表连成环

让尾节点指回头节点：

```text
tail->next = head
```

这样链表就变成了一个环。

### 第四步：找到新的尾节点

假设链表长度是 `n`，向右旋转 `k` 次后：

- 新尾节点应该是原链表中的第 `n - k - 1` 个节点
- 新头节点就是新尾节点的下一个节点

为什么是 `n - k - 1`？

因为右移 `k` 位后，最后 `k` 个节点会跑到最前面。
所以新的分界点，正好在原链表从前往后第 `n - k` 个节点之前。

也就是说：

- 第 `n - k - 1` 个节点是新尾
- 第 `n - k` 个节点是新头

### 第五步：断开环

找到新头后，先记录：

```text
newHead = newTail->next
```

然后把：

```text
newTail->next = nullptr
```

这样环就被切开，链表重新恢复成正常单链表，且已经是旋转后的结果。

### 以示例 1 为例

```text
head = [1,2,3,4,5], k = 2
```

链表长度：

```text
n = 5
```

有效旋转次数：

```text
k = 2 % 5 = 2
```

所以：

- 新尾节点位置是 `5 - 2 - 1 = 2`
- 也就是值为 `3` 的节点
- 新头节点就是 `4`

把链表先连成环：

```text
1 -> 2 -> 3 -> 4 -> 5
               ^    |
               |____|
```

然后在 `3` 后面断开，就得到：

```text
4 -> 5 -> 1 -> 2 -> 3
```

### 复杂度分析

- **时间复杂度：** `O(n)`，只需要遍历链表常数次
- **空间复杂度：** `O(1)`，只使用常数个额外指针

## 代码

### C++

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        if (head == nullptr || head->next == nullptr || k == 0) {
            return head;
        }

        int n = 1;
        ListNode* tail = head;
        while (tail->next != nullptr) {
            tail = tail->next;
            n++;
        }

        k %= n;
        if (k == 0) {
            return head;
        }

        tail->next = head;

        int stepsToNewTail = n - k - 1;
        ListNode* newTail = head;
        for (int i = 0; i < stepsToNewTail; i++) {
            newTail = newTail->next;
        }

        ListNode* newHead = newTail->next;
        newTail->next = nullptr;

        return newHead;
    }
};
```

### Python

```python
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next


class Solution:
    def rotateRight(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if not head or not head.next or k == 0:
            return head

        n = 1
        tail = head
        while tail.next:
            tail = tail.next
            n += 1

        k %= n
        if k == 0:
            return head

        tail.next = head

        steps_to_new_tail = n - k - 1
        new_tail = head
        for _ in range(steps_to_new_tail):
            new_tail = new_tail.next

        new_head = new_tail.next
        new_tail.next = None

        return new_head
```
