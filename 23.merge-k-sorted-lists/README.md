# 23. 合并 K 个升序链表 (Merge k Sorted Lists)

## 题目描述

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

**示例 1：**

```text
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到：
1->1->2->3->4->4->5->6
```

**示例 2：**

```text
输入：lists = []
输出：[]
```

**示例 3：**

```text
输入：lists = [[]]
输出：[]
```

**提示：**

- `k == lists.length`
- `0 <= k <= 10^4`
- `0 <= lists[i].length <= 500`
- `-10^4 <= lists[i][j] <= 10^4`
- `lists[i]` 按升序排列
- `lists[i].length` 的总和不超过 `10^4`

## 算法知识

### 链表合并

这道题是第 21 题“合并两个有序链表”的扩展版。

如果我们已经会合并两个有序链表，那么一个自然想法就是：

- 先合并前两个链表
- 再把结果和第三个链表合并
- 再和第四个链表合并

这样可以做出来，但效率不是最优。

### 分治

分治（Divide and Conquer）的核心思想是：

1. 把大问题拆成若干个规模更小的同类子问题
2. 递归解决这些子问题
3. 再把子问题的答案合并起来

本题中，“合并 K 个链表”可以拆成：

- 合并左半部分链表
- 合并右半部分链表
- 最后把左右两个合并结果再合并一次

这种做法和归并排序的结构很像。

## 解法：分治合并（推荐）

### 思路

定义函数 `mergeKLists(lists)`：

1. 如果链表数组为空，直接返回空链表
2. 如果只剩一个链表，直接返回它
3. 否则把链表数组分成左右两半
4. 递归合并左半部分
5. 递归合并右半部分
6. 最后调用“合并两个有序链表”的函数，把两部分结果合并

这里的关键是复用第 21 题的双指针链表合并逻辑。

### 为什么分治更好？

假设一共有 `k` 个链表，总节点数是 `N`。

如果按顺序一个一个合并：

- 第一次合并 2 个链表
- 第二次拿结果再和第 3 个链表合并
- ...

前面的节点会被反复参与合并，效率会偏低。

而分治会让每个节点只在每一层合并中被处理一次，总共有 `log k` 层，因此总体复杂度更优。

### 复杂度分析

- **时间复杂度**：O(N log k)，其中 `N` 是所有链表节点总数，`k` 是链表个数。
- **空间复杂度**：O(log k)，主要来自递归调用栈。不计返回链表本身。

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
    ListNode* mergeTwoLists(ListNode* a, ListNode* b) {
        ListNode dummy(0);
        ListNode* cur = &dummy;

        while (a && b) {
            if (a->val <= b->val) {
                cur->next = a;
                a = a->next;
            } else {
                cur->next = b;
                b = b->next;
            }
            cur = cur->next;
        }

        cur->next = a ? a : b;
        return dummy.next;
    }

    ListNode* merge(vector<ListNode*>& lists, int left, int right) {
        if (left == right) {
            return lists[left];
        }

        int mid = left + (right - left) / 2;
        ListNode* l1 = merge(lists, left, mid);
        ListNode* l2 = merge(lists, mid + 1, right);
        return mergeTwoLists(l1, l2);
    }

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if (lists.empty()) {
            return nullptr;
        }
        return merge(lists, 0, lists.size() - 1);
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
    def mergeTwoLists(self, a: Optional[ListNode], b: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(0)
        cur = dummy

        while a and b:
            if a.val <= b.val:
                cur.next = a
                a = a.next
            else:
                cur.next = b
                b = b.next
            cur = cur.next

        cur.next = a if a else b
        return dummy.next

    def merge(self, lists: List[Optional[ListNode]], left: int, right: int) -> Optional[ListNode]:
        if left == right:
            return lists[left]

        mid = left + (right - left) // 2
        l1 = self.merge(lists, left, mid)
        l2 = self.merge(lists, mid + 1, right)
        return self.mergeTwoLists(l1, l2)

    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        if not lists:
            return None
        return self.merge(lists, 0, len(lists) - 1)
```
