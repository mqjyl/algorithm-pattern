---
description: 这部分主要考察线性表的操作，包括数组、单链表和双向链表等。
---

# 线性表

## 基本技能

* null/nil 异常处理
* dummy node 哑巴节点
* 快慢指针
* 对结点进行增、删、改、查等操作
* 翻转链表
* 合并两个链表
* 找到链表的中间节点

## 基础题型

### [remove-duplicates-from-sorted-list](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

> 给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

```cpp
ListNode* deleteDuplicates(ListNode* head) {
    ListNode *ptr = head;
    //注意访问x.val和x.next得保证x != NULL.
    while(ptr && ptr->next){
        if(ptr->next->val == ptr->val){
            ptr->next = ptr->next->next;
        }else{
            ptr = ptr->next;
        }
    }
    return head;
}
```

### [remove-duplicates-from-sorted-list-ii](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

> 给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现的数字。
>
> 思路：链表头可能会出现重复的元素，这时候需要修改链表头。

```cpp
ListNode* deleteDuplicates(ListNode* head) {
    if(!head)
        return head;
    //设置一个哑结点，可以避免边界问题
    ListNode *prev_ptr = new ListNode(head->val + 1);
    prev_ptr->next = head;
    head = prev_ptr;
    //后续的处理逻辑是ptr往前遍历，ptr指向的结点只有两种情况，删和不删，
    //删的情况交给prev_ptr去处理
    ListNode *ptr = head;
    while(ptr && ptr->next){
        if(ptr->next->val == ptr->val){
            while(ptr->next && ptr->val == ptr->next->val){
                ptr = ptr->next;
            }
            prev_ptr->next = ptr->next;
        }
        ptr = ptr->next;
        //做判断的目的是看ptr和prev_ptr中间有没有保留的元素，有的话及时跳过
        if(prev_ptr->next && prev_ptr->next->next == ptr){
            prev_ptr = prev_ptr->next;
        }
    }

    prev_ptr = head;
    head = head->next;
    delete prev_ptr;
    return head;
}
```

### [reverse-linked-list](https://leetcode-cn.com/problems/reverse-linked-list/)

> 反转一个链表

```cpp
ListNode* reverseList(ListNode* head) {
    if(!(head && head->next))
        return head;
    ListNode *tmpHead = head->next;
    ListNode *newHead = head;
    newHead->next = NULL;
    while(tmpHead){
        ListNode *ptr = tmpHead;
        tmpHead = tmpHead->next;
        ptr->next = newHead;
        newHead = ptr;
    }
    head = newHead;
    return head;
}
// 递归实现
/*
ListNode* reverseList(ListNode* head) {
    if(!(head && head->next))
        return head;
    ListNode *ptr = head;
    ListNode *nextPtr = head->next;
    ListNode *subHead = reverseList(nextPtr);
    ptr->next = NULL;
    nextPtr->next = ptr;
    return subHead;
}
*/
```

### [reverse-linked-list-ii](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

> 反转从位置 _m_ 到 _n_ 的链表。请使用一趟扫描完成反转。
>
> 方法一：迭代法（下面的实现），先拆分再合并，需要注意的是从第一个节点开始反转的特例（可以通过在原始链表开头加入一个新节点（即哑节点）的方式）。
>
> 方法二：双指针，头插法（见下图）
>
> 方法三：交换值而非链接，递归和迭代都容易实现。

```cpp
ListNode* reverseBetween(ListNode* head, int m, int n) {
    if(!head->next)
        return head;
    ListNode *ptr = head;
    // 对于 m=1 的情况要单独处理，因为这时候不需要下面的pre_tail
    if(m == 1){
        ListNode *pre_ptr= head->next;
        ptr->next = NULL;
        while(pre_ptr && n > 1){
            ListNode *tmp_ptr = pre_ptr;
            pre_ptr = pre_ptr->next;
            tmp_ptr->next = ptr;
            ptr = tmp_ptr;
            n--;
        }
        head->next = pre_ptr;
        return ptr;
    }
    while(m > 2){// m 的前一个要记录在pre_tail中
        ptr = ptr->next;
        m--; n--;
    }
    // 开始反转
    ListNode *pre_tail = ptr;
    ListNode *in_tail = ptr->next;
    ListNode *pre_ptr = ptr->next;
    ptr = ptr->next->next;
    while(n > 2 && ptr){
        ListNode *tmp_ptr = ptr;
        ptr = ptr->next;
        tmp_ptr->next = pre_ptr;
        pre_ptr = tmp_ptr;
        n--;
    }
    pre_tail->next = pre_ptr;
    in_tail->next = ptr;
    return head;
}
```

### [merge-two-sorted-lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```cpp
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode *head = new ListNode(0);
    ListNode *ptr = head;
    while(l1 && l2){
        if(l1->val > l2->val){
            ptr->next = l2;
            l2 = l2->next;
        }else{
            ptr->next = l1;
            l1 = l1->next;
        }
        ptr = ptr->next;
    }
    if(l1){
        ptr->next = l1;
    }
    if(l2){
        ptr->next = l2;
    }
    ptr = head;
    head = head->next;
    delete ptr;
    return head;
}
```

### [partition-list](https://leetcode-cn.com/problems/partition-list/)

给定一个链表和一个特定值 x，对链表进行分隔，使得所有小于 _x_ 的节点都在大于或等于 _x_ 的节点之前。

> 思路：将小于或大于 x 的节点，放到另外一个链表，最后连接这两个链表。

> 哑**巴节点使用场景 ：当头节点不确定的时候，使用哑巴节点**

```cpp
ListNode* partition(ListNode* head, int x) {
    if(!(head && head->next)){
        return head;
    }
    ListNode *dummy = new ListNode(0);
    dummy->next = head;
    bool flag = true;
    ListNode *tmp_head_small, *tmp_ptr_small;
    ListNode *ptr = dummy;
    while(ptr->next){
        if(ptr->next->val < x){
            if(flag){
                tmp_head_small = tmp_ptr_small = ptr->next;
                flag = !flag;
            }else{
                tmp_ptr_small->next = ptr->next;
                tmp_ptr_small = tmp_ptr_small->next;
            }
            ptr->next = ptr->next->next;
        }else{
            ptr = ptr->next;
        }
    }
    if(tmp_head_small && tmp_ptr_small){
        tmp_ptr_small->next = dummy->next;
        dummy->next = tmp_head_small;
    }
    return dummy->next;
}
```

### \*\*\*\*[**swap-nodes-in-pairs**](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)\*\*\*\*

> 给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。**不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

```cpp
// 递归
ListNode* swapOnePair(ListNode* pre_head){
    if(!(pre_head->next && pre_head->next->next))
        return pre_head;
    ListNode *tmp = pre_head;
    ListNode *ptr = pre_head->next;
    tmp->next = ptr->next;
    ptr->next = ptr->next->next;
    tmp = tmp->next;
    tmp->next = ptr;
    tmp = tmp->next;
    swapOnePair(tmp);
    return pre_head->next;
}

ListNode* swapPairs(ListNode* head) {
    if(!(head && head->next))
        return head;
    ListNode *dummy = new ListNode(0);
    dummy->next = head;
    return swapOnePair(dummy);
}
// 迭代
ListNode* swapPairs(ListNode* head) {
    if(!(head && head->next))
        return head;
    ListNode *dummy = new ListNode(0);
    dummy->next = head;
    ListNode *ptr = dummy;
    ListNode *tmp = ptr;
    while(ptr->next){
        ptr = ptr->next;
        if(ptr->next){
            tmp->next = ptr->next;
            ptr->next = ptr->next->next;
            tmp = tmp->next;
            tmp->next = ptr;
            tmp = tmp->next;
        }
    }
    return dummy->next;*/
}
```

### [sort-list](https://leetcode-cn.com/problems/sort-list/)

> 在  __$$O(n log n)$$  时间复杂度和常数级空间复杂度下，对链表进行排序。

```cpp

```

