---
title: "链表部分"
date: 2020-09-11T08:16:07+08:00
draft: true
---

## 判断链表是否有环

### 方法

快慢指针

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode * p1 = head, * p2 = head;
        while(p2 != NULL && p2->next != NULL)
        {
            p1 = p1->next;
            p2 = p2->next->next;
            if(p1 == p2)
            {
                return true;
            }
        }
        return false;
    }
};
```

## 链表反转

最简单的办法，就是将链表的所有节点存数组里面，然后逆序存到新的链表。

好一点的办法，就是每个元素用头插法。

**初始化：3个指针**

1）pre指针指向已经反转好的链表的最后一个节点，最开始没有反转，所以指向nullptr

2）cur指针指向待反转链表的第一个节点，最开始第一个节点待反转，所以指向head

3）nex指针指向待反转链表的第二个节点，目的是保存链表，因为cur改变指向后，后面的链表则失效了，所以需要保存

**接下来，循环执行以下三个操作**

1）nex = cur->next, 保存作用

2）cur->next = pre 未反转链表的第一个节点的下个指针指向已反转链表的最后一个节点

3）pre = cur， cur = nex; 指针后移，操作下一个未反转链表的第一个节点

```cpp
class Solution {
public:
    ListNode* ReverseList(ListNode* pHead) {
        ListNode *pre = nullptr;
        ListNode *cur = pHead;
        ListNode *nex = nullptr; // 这里可以指向nullptr，循环里面要重新指向
        while (cur) {
            nex = cur->next;
            cur->next = pre;
            pre = cur;
            cur = nex;
        }
        return pre;
    }
};
```

## 输出倒数第k个节点的值

```cpp
class Solution {
public:
    ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
        int length = 0;
        ListNode * p = pListHead;
        if(p == NULL)
        {
            return p;
        }
        while(p->next !=NULL)
        {
            p = p->next;
            length++;
        }
        if(k>length+1)
        {
            return NULL;
        }
        int dest = length - k+1;
        p = pListHead;
        for(int i=0;i<dest;i++)
        {
            p = p->next;
        }
        return p;
    }
};
```

注意长度问题，和判断链表为空的问题。

