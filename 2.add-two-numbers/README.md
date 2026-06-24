# 2. 两数相加 (Add Two Numbers)

## 题目描述

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**示例 1：**

![](https://fastly.jsdelivr.net/gh/doocs/leetcode@main/solution/0000-0099/0002.Add%20Two%20Numbers/images/addtwonumber1.jpg)

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

**示例 2：**

```
输入：l1 = [0], l2 = [0]
输出：[0]
```

**示例 3：**

```
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出：[8,9,9,9,0,0,0,1]
```

**提示：**

- 每个链表中的节点数在范围 `[1, 100]` 内
- `0 <= Node.val <= 9`
- 题目数据保证列表表示的数字不含前导零

## 算法知识

### 链表 (Linked List)

**链表** 是一种线性数据结构，每个节点包含 **值** 和指向 **下一个节点** 的指针（`next`）。

- **单链表**：每个节点只有 `next` 指针，只能从头到尾单向遍历
- **与数组对比**：数组插入/删除需要移动 O(n) 个元素，链表插入/删除只需修改指针，O(1)；但链表不支持随机访问（不能像数组那样 `arr[5]` 直接取第 6 个元素）
- **本题链表**：LeetCode 预定义的 `ListNode` 结构：
  - C++：`ListNode(int x) : val(x), next(nullptr) {}`
  - Python：`ListNode(val=0, next=None)`
- **哑节点 (dummy node)**：在链表头部加一个虚拟节点，避免处理头节点为空的边界情况，最终返回 `dummy->next` 即可

### 模拟加法

题目中数字按 **逆序** 存储，即链表头是最低位（个位），正好符合我们做竖式加法时 **从低位到高位** 的计算顺序。

竖式加法步骤：
1. 对齐两个数的当前位，连同进位相加
2. 当前位 = 和 mod 10
3. 进位 = 和 / 10（向下取整）
4. 移动到下一位，重复以上步骤

因为两个链表长度可能不同，短链表高位视为 0 即可。

## 解法

同时遍历两个链表，用变量 `carry` 记录进位。每次取两链表当前节点值（空则取 0），加上进位后，当前位存入结果链表，进位更新。遍历结束后若进位为 1，再补一个节点。

**时间复杂度** O(max(m, n))，**空间复杂度** O(1)（不计结果空间）。

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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        // 哑节点，简化头节点处理
        ListNode* dummy = new ListNode(0);
        ListNode* cur = dummy;
        int carry = 0;

        // 两个链表都遍历完且无进位时才停止
        while (l1 || l2 || carry) {
            // 取当前位值，链表为空则取 0
            int x = l1 ? l1->val : 0;
            int y = l2 ? l2->val : 0;
            int sum = x + y + carry;

            // 当前位 = 和 mod 10，进位 = 和 / 10
            carry = sum / 10;
            cur->next = new ListNode(sum % 10);
            cur = cur->next;

            // 指针后移
            l1 = l1 ? l1->next : nullptr;
            l2 = l2 ? l2->next : nullptr;
        }

        return dummy->next;
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
    def addTwoNumbers(
        self, l1: Optional[ListNode], l2: Optional[ListNode]
    ) -> Optional[ListNode]:
        # 哑节点，简化头节点处理
        dummy = ListNode(0)
        cur = dummy
        carry = 0

        # 两个链表都遍历完且无进位时才停止
        while l1 or l2 or carry:
            # 取当前位值，链表为空则取 0
            x = l1.val if l1 else 0
            y = l2.val if l2 else 0
            total = x + y + carry

            # 当前位 = 和 mod 10，进位 = 和 // 10
            carry = total // 10
            cur.next = ListNode(total % 10)
            cur = cur.next

            # 指针后移
            l1 = l1.next if l1 else None
            l2 = l2.next if l2 else None

        return dummy.next
```
