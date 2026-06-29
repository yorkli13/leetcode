# 25. K 个一组翻转链表 (Reverse Nodes in k-Group)

## 题目描述

给你链表的头节点 `head`，每 `k` 个节点一组进行翻转，请你返回修改后的链表。

`k` 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 `k` 的整数倍，那么请将最后剩余的节点保持原有顺序。

你不能只是单纯地改变节点内部的值，而是需要实际进行节点交换。

**示例 1：**

```text
输入：head = [1,2,3,4,5], k = 2
输出：[2,1,4,3,5]
```

**示例 2：**

```text
输入：head = [1,2,3,4,5], k = 3
输出：[3,2,1,4,5]
```

**提示：**

- 链表中的节点数目为 `n`
- `1 <= k <= n <= 5000`
- `0 <= Node.val <= 1000`

## 算法知识

### 链表局部翻转

链表翻转的核心是修改指针方向。

如果只翻转一段链表，比如：

```text
1 -> 2 -> 3
```

翻转后会变成：

```text
3 -> 2 -> 1
```

本题比普通链表翻转更进一步，它要求：

- 不是整条链表一起翻转
- 而是每 `k` 个节点作为一组独立翻转

### 哑节点

因为第一组翻转后，头节点很可能发生变化，所以使用哑节点可以让整个过程更统一。

有了哑节点后，每次只需要维护“当前这一组前面的节点”即可，不需要特判头节点。

## 解法：按组翻转链表（推荐）

### 思路

整个过程分成三步：

1. 先检查当前这一组是否有 `k` 个节点
2. 如果不足 `k` 个，直接结束，保持原顺序
3. 如果足够，就把这一组单独翻转，再接回原链表

我们维护一个指针 `groupPrev`，表示当前待翻转分组的前一个节点。

对于每一组：

1. 从 `groupPrev` 开始向后走 `k` 步，找到这一组的最后一个节点 `kth`
2. 记录下一组的起点 `groupNext = kth->next`
3. 翻转 `[groupPrev->next, kth]` 这一段
4. 翻转完成后，把前后链表重新接起来
5. 更新 `groupPrev` 到这一组翻转后的末尾，继续处理下一组

### 如何翻转一组？

假设当前一组是：

```text
a -> b -> c -> groupNext
```

翻转后应该变成：

```text
c -> b -> a -> groupNext
```

做法和普通链表翻转类似：

1. 用 `prev = groupNext`
2. 从当前组的第一个节点开始，依次让每个节点指向 `prev`
3. 直到处理完整组

这样翻转后，原来的第一个节点就会自动接回 `groupNext`。

### 为什么这样做可行？

因为我们每次只在“完整的 k 个节点”这一小段内部修改指针，翻转完成后再把这一段重新接回整条链表。

同时：

- 不足 `k` 个的最后一段不会被翻转
- 每一组翻转后，链表其余部分结构仍然保持正确

所以整个过程可以逐组独立完成，最终得到符合题意的结果。

### 复杂度分析

- **时间复杂度**：O(n)，其中 `n` 是链表节点总数。每个节点最多被访问常数次。
- **空间复杂度**：O(1)，只使用了若干辅助指针。

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
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode dummy(0);
        dummy.next = head;
        ListNode* groupPrev = &dummy;

        while (true) {
            // 找到当前这一组的第 k 个节点
            ListNode* kth = groupPrev;
            for (int i = 0; i < k && kth; i++) {
                kth = kth->next;
            }
            if (!kth) {
                break;  // 剩余节点不足 k 个，不再翻转
            }

            ListNode* groupNext = kth->next;

            // 翻转这一组
            ListNode* prev = groupNext;
            ListNode* cur = groupPrev->next;
            while (cur != groupNext) {
                ListNode* nxt = cur->next;
                cur->next = prev;
                prev = cur;
                cur = nxt;
            }

            // 重新接回链表
            ListNode* newGroupHead = kth;
            ListNode* newGroupTail = groupPrev->next;
            groupPrev->next = newGroupHead;
            groupPrev = newGroupTail;
        }

        return dummy.next;
    }
};
```

### Python

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

class Solution:
    def reverseKGroup(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        dummy = ListNode(0)
        dummy.next = head
        group_prev = dummy

        while True:
            # 找到当前这一组的第 k 个节点
            kth = group_prev
            for _ in range(k):
                kth = kth.next
                if not kth:
                    return dummy.next

            group_next = kth.next

            # 翻转这一组
            prev = group_next
            cur = group_prev.next
            while cur != group_next:
                nxt = cur.next
                cur.next = prev
                prev = cur
                cur = nxt

            # 重新接回链表
            new_group_head = kth
            new_group_tail = group_prev.next
            group_prev.next = new_group_head
            group_prev = new_group_tail
```
