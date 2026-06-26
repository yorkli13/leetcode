# 21. 合并两个有序链表 (Merge Two Sorted Lists)

## 题目描述

将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

**示例 1：**

```text
输入：list1 = [1,2,4], list2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

**示例 2：**

```text
输入：list1 = [], list2 = []
输出：[]
```

**示例 3：**

```text
输入：list1 = [], list2 = [0]
输出：[0]
```

**提示：**

- 两个链表的节点数目范围是 `[0, 50]`
- `-100 <= Node.val <= 100`
- `list1` 和 `list2` 均按非递减顺序排列

## 算法知识

### 链表

链表是一种由节点组成的线性数据结构，每个节点包含：

- 当前节点的值
- 指向下一个节点的指针 `next`

和数组不同，链表不支持随机访问，但在“接链”“断链”这类操作上很方便，因为只需要修改指针。

### 哑节点

哑节点（dummy node）是链表题里非常常见的技巧。

它的作用是：

- 先创建一个额外的虚拟头节点
- 用一个指针 `cur` 从这个虚拟节点开始向后连接真正的结果节点

这样做的好处是不用单独处理“结果链表的第一个节点是谁”这个特殊情况，代码会更统一。

## 解法：双指针模拟合并（推荐）

### 思路

因为两个链表本身已经有序，所以每次只需要比较当前两个节点的值：

1. 谁更小，就先把谁接到结果链表后面
2. 对应链表指针后移一位
3. 结果链表指针 `cur` 也后移一位

当其中一个链表遍历完后：

- 另一个链表剩下的部分一定已经有序
- 可以直接整体接到结果链表后面

### 为什么这样做可行？

因为两个链表都已经升序排列，所以当前两个头节点中较小的那个，一定是剩余所有节点里最小的。

因此，每次把较小节点接到结果链表后面，结果链表就始终保持有序。

不断重复这个过程，直到所有节点都被接完，就得到了完整的升序链表。

### 复杂度分析

- **时间复杂度**：O(m + n)，其中 `m` 和 `n` 分别是两个链表的长度。每个节点最多访问一次。
- **空间复杂度**：O(1)，只使用了若干辅助指针，不计返回链表本身。

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
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode dummy(0);
        ListNode* cur = &dummy;

        while (list1 && list2) {
            if (list1->val <= list2->val) {
                cur->next = list1;
                list1 = list1->next;
            } else {
                cur->next = list2;
                list2 = list2->next;
            }
            cur = cur->next;
        }

        // 把剩余未遍历完的链表直接接到后面
        cur->next = list1 ? list1 : list2;

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
    def mergeTwoLists(
        self, list1: Optional[ListNode], list2: Optional[ListNode]
    ) -> Optional[ListNode]:
        dummy = ListNode(0)
        cur = dummy

        while list1 and list2:
            if list1.val <= list2.val:
                cur.next = list1
                list1 = list1.next
            else:
                cur.next = list2
                list2 = list2.next
            cur = cur.next

        # 把剩余未遍历完的链表直接接到后面
        cur.next = list1 if list1 else list2

        return dummy.next
```
