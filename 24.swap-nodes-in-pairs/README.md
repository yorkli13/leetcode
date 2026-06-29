# 24. 两两交换链表中的节点 (Swap Nodes in Pairs)

## 题目描述

给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。

你必须在不修改节点内部值的情况下完成本题，也就是说，只能进行节点交换。

**示例 1：**

```text
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```

**示例 2：**

```text
输入：head = []
输出：[]
```

**示例 3：**

```text
输入：head = [1]
输出：[1]
```

**提示：**

- 链表中节点的数目在范围 `[0, 100]` 内
- `0 <= Node.val <= 100`

## 算法知识

### 链表指针操作

链表题的核心通常不是数值计算，而是指针关系的调整。

本题要求“两两交换节点”，比如：

```text
1 -> 2 -> 3 -> 4
```

交换前两个节点后会变成：

```text
2 -> 1 -> 3 -> 4
```

再交换后两个节点后变成：

```text
2 -> 1 -> 4 -> 3
```

注意，题目要求交换的是节点本身，而不是简单交换节点的值。

### 哑节点

因为链表头节点在交换第一对节点后会发生变化，所以使用哑节点会让代码更统一。

哑节点的作用是：

- 在链表最前面放一个虚拟节点
- 用它来稳定地指向真正的头节点

这样即使原来的头节点被交换到第二个位置，也不需要专门写额外逻辑处理。

## 解法：链表指针模拟（推荐）

### 思路

每次处理一对相邻节点：

假设当前结构是：

```text
prev -> first -> second -> nextPair
```

我们希望交换后变成：

```text
prev -> second -> first -> nextPair
```

具体操作步骤：

1. 记录当前一对节点的第一个节点 `first`
2. 记录第二个节点 `second`
3. 让 `prev->next = second`
4. 让 `first->next = second->next`
5. 让 `second->next = first`
6. 然后把 `prev` 移动到交换后的后一个节点，也就是 `first`

不断重复，直到剩余节点少于两个。

### 为什么这样做可行？

因为每次交换时，我们只改动当前这一对节点及其与前后部分的连接关系：

- 前面的部分通过 `prev` 接上新的头节点 `second`
- 中间两节点完成交换
- 后面的部分仍然通过 `first->next` 接回去

这样每次只处理一个局部结构，不会破坏其他部分，整条链表最终就会按要求完成两两交换。

### 复杂度分析

- **时间复杂度**：O(n)，其中 `n` 是链表节点数，每个节点最多访问常数次。
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
    ListNode* swapPairs(ListNode* head) {
        ListNode dummy(0);
        dummy.next = head;
        ListNode* prev = &dummy;

        while (prev->next && prev->next->next) {
            ListNode* first = prev->next;
            ListNode* second = first->next;

            prev->next = second;
            first->next = second->next;
            second->next = first;

            prev = first;
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
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(0)
        dummy.next = head
        prev = dummy

        while prev.next and prev.next.next:
            first = prev.next
            second = first.next

            prev.next = second
            first.next = second.next
            second.next = first

            prev = first

        return dummy.next
```
