# 19. 删除链表的倒数第 N 个结点

## 题目描述

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

**示例 1：**

![示例1图片](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

**示例 2：**

```
输入：head = [1], n = 1
输出：[]
```

**示例 3：**

```
输入：head = [1,2], n = 1
输出：[1]
```

**提示：**

- 链表中结点的数目为 `sz`
- `1 <= sz <= 30`
- `0 <= Node.val <= 100`
- `1 <= n <= sz`

**进阶：** 你能尝试使用一趟扫描实现吗？

## 算法知识

### 双指针（快慢指针）

双指针是链表问题中常用的技巧，特别适用于需要找到链表中特定位置节点的场景。

**核心思想：**
- 使用两个指针 `fast` 和 `slow`，让它们保持固定的距离（本题中距离为 `n`）
- 先让 `fast` 指针向前移动 `n` 步，此时 `fast` 和 `slow` 相距 `n` 个节点
- 然后同时移动两个指针，直到 `fast` 到达链表末尾
- 此时 `slow` 指针正好指向要删除节点的前一个节点

**为什么这样做有效？**
- 链表总长度为 `L`，要删除倒数第 `n` 个节点，即正数第 `L-n+1` 个节点
- `fast` 先走 `n` 步后，当 `fast` 到达末尾（走了 `L` 步）时，`slow` 走了 `L-n` 步
- `slow` 正好指向要删除节点的前一个节点，方便进行删除操作

**边界情况处理：**
- 删除头节点的情况：当 `fast` 先走 `n` 步后直接到达末尾，说明要删除的是头节点
- 使用虚拟头节点（dummy node）可以统一处理删除头节点和其他节点的情况

## 解法

### 方法一：两次遍历

**思路：**
1. 第一次遍历计算链表长度 `L`
2. 第二次遍历找到第 `L-n` 个节点（要删除节点的前驱）
3. 删除目标节点

**复杂度分析：**
- 时间复杂度：O(L)，需要遍历两次链表
- 空间复杂度：O(1)，只使用了常数级别的额外空间

### 方法二：双指针一次遍历（推荐）

**思路：**
1. 创建虚拟头节点 `dummy`，指向 `head`
2. 初始化 `fast` 和 `slow` 都指向 `dummy`
3. 让 `fast` 先向前移动 `n+1` 步（确保 `slow` 指向要删除节点的前一个节点）
4. 同时移动 `fast` 和 `slow`，直到 `fast` 为 `null`
5. 删除 `slow->next` 节点
6. 返回 `dummy->next`

**复杂度分析：**
- 时间复杂度：O(L)，只需遍历一次链表
- 空间复杂度：O(1)，只使用了常数级别的额外空间

## 代码

### C++ 实现

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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        // 创建虚拟头节点，简化边界情况处理
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        
        ListNode* fast = dummy;
        ListNode* slow = dummy;
        
        // fast 先移动 n+1 步，保持 fast 和 slow 之间距离为 n
        for (int i = 0; i <= n; i++) {
            fast = fast->next;
        }
        
        // 同时移动 fast 和 slow，直到 fast 到达末尾
        while (fast != nullptr) {
            fast = fast->next;
            slow = slow->next;
        }
        
        // 删除 slow->next 节点
        ListNode* toDelete = slow->next;
        slow->next = toDelete->next;
        delete toDelete;
        
        // 保存结果并释放虚拟头节点
        ListNode* result = dummy->next;
        delete dummy;
        
        return result;
    }
};
```

### Python 实现

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
        # 创建虚拟头节点，简化边界情况处理
        dummy = ListNode(0, head)
        fast = dummy
        slow = dummy
        
        # fast 先移动 n+1 步，保持 fast 和 slow 之间距离为 n
        for _ in range(n + 1):
            fast = fast.next
        
        # 同时移动 fast 和 slow，直到 fast 到达末尾
        while fast:
            fast = fast.next
            slow = slow.next
        
        # 删除 slow.next 节点
        slow.next = slow.next.next
        
        return dummy.next
```