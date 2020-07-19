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

无法高效获取长度，无法根据偏移快速访问元素，是链表的两个劣势。然而面试的时候经常碰见诸如获取倒数第k个元素，获取中间位置的元素，判断链表是否存在环，判断环的长度等和长度与位置有关的问题。这些问题都可以通过灵活运用双指针来解决。 **双指针并不是固定的公式，而是一种思维方式。**

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

给定一个链表和一个特定值 x，对链表进行分隔，使得所有小于 x 的节点都在大于或等于 x 的节点之前。

> 思路：将小于 `x` 的节点，放到另外一个链表，最后连接这两个链表。

> 哑**巴节点使用场景 ：当头节点不确定的时候，使用哑巴节点**

```cpp
ListNode* partition(ListNode* head, int x) {
    if(!head || !head->next){
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
>
> 方法一、归并排序递归实现。

> * 快慢指针 判断 `quickptr` 及 `quickptr.next` 是否为 `NULL` 值
> * 递归 `merge` 前需要断开中间节点
> * 递归出口为 `head` 为 `NULL` 或者 `head.next` 为 `NULL`

```cpp
ListNode* sortList(ListNode* head) {
    if(!head || !head->next)
        return head;
    // 拆分
    ListNode *quickptr=head->next, *slowptr=head;
    // 若quickptr=head，当原始链表长度为2时，会进入无线递归。
    while(quickptr && quickptr->next){
        quickptr = quickptr->next->next;
        slowptr = slowptr->next;
    }
    ListNode *tmpHead = slowptr->next;
    slowptr->next = NULL;
    ListNode *head1 = sortList(head);
    ListNode *head2 = sortList(tmpHead);
    // 合并
    return mergeTwoLists(head1, head2);
}
// quickptr 如果初始化为 head.next，则中点在 slowptr.Next
// quickptr 初始化为 head，则中点在 slowptr
// 在快慢指针的某些问题中，即不涉及删除和断链的问题，如链表的环问题中，quickptr可以设置为head，
// 但是这里不可以，因为12行不能写成slowptr = NULL;
```

> 方法二、

### [reorder-list](https://leetcode-cn.com/problems/reorder-list/)

给定一个单链表 `L：L0→L1→…→Ln-1→Ln` ， 将其重新排列后变为： `L0→Ln→L1→Ln-1→L2→Ln-2→…`

不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

> 除了暴力外，有三种解法可以将时间复杂度降到 $$O(n)$$ 。依然用快慢指针：

> 方法一：利用栈，存储前半部分的链表。
>
> 方法二：在第一次遍历的时候直接将前半部分链表反转，然后两个子链表交替链接（头插）。**或找到中点断开，翻转后面部分，然后合并前后两个链表（尾插）。（此方法最好）**
>
> 方法三：深度递归。

```cpp
// 方法一
void reorderList(ListNode* head) {
    if(!head || !head->next)
        return;
    ListNode *quickptr=head->next, *slowptr=head;
    std::stack<ListNode *> istack;
    while(quickptr && quickptr->next){
        quickptr = quickptr->next->next;
        istack.push(slowptr);
        slowptr = slowptr->next;
    }
    ListNode *tmpHead = slowptr->next;
    ListNode *tmpPtr = slowptr;
    ListNode *newHead = tmpPtr;
    if(!quickptr){ // 奇数个
        tmpPtr->next = NULL;
    }else{
        tmpHead = tmpHead->next;
        tmpPtr->next->next = NULL;
    }
    while(!istack.empty()){
        tmpPtr = istack.top();
        istack.pop();
        ListNode *tmp = tmpHead->next;
        tmpHead->next = newHead;
        tmpPtr->next = tmpHead;
        tmpHead = tmp;
        newHead = tmpPtr;
    }
    head = newHead;
}
// 方法二
void reorderList(ListNode* head) {
    if(!head || !head->next)
        return;
    ListNode *quickptr=head->next, *slowptr=head;
    while(quickptr && quickptr->next){
        quickptr = quickptr->next->next;
        slowptr = slowptr->next;
    }
    ListNode *rightHead = slowptr->next;
    slowptr->next = NULL;
    rightHead = reverseList(rightHead);
    // 合并
    ListNode *leftHead = head;
    ListNode *tmpPtr = leftHead;
    while(leftHead){
        tmpPtr = leftHead;
        leftHead = leftHead->next;
        tmpPtr->next = rightHead;
        if(rightHead){ // 奇数个的情况，所以需要判断
            rightHead = rightHead->next;
            tmpPtr->next->next = leftHead;
        }
    }
}
```

### [linked-list-cycle](https://leetcode-cn.com/problems/linked-list-cycle/)

给定一个链表，判断链表中是否有环。

> 方法一、快慢指针法，快慢指针的特性 —— 每轮移动之后两者的距离会加一。如果一个链表存在环，那么快慢指针必然会相遇。
>
> 方法二、哈希表法， **通过`hash`表来检测节点之前是否被访问过**，来判断链表是否成环。

```cpp
// 方法一
bool hasCycle(ListNode *head) {
    if(!head || !head->next)
        return false;
    ListNode *quickptr=head->next, *slowptr=head;
    while(quickptr && quickptr->next){
        if(quickptr == slowptr){
            return true;
        }
        quickptr = quickptr->next->next;
        slowptr = slowptr->next;
    }
    return false;
}
// 方法二
bool hasCycle(ListNode *head) {
    if(!head || !head->next)
        return false;
    std::set<ListNode *> iset;
    ListNode *ptr = head;
    while(ptr){
        if(iset.count(ptr) > 0){
            return true;
        }
        iset.insert(ptr);
        ptr = ptr->next;
    }
    return false;
}
```

### [linked-list-cycle-ii](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

 给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 `null`。

> Floyd 的算法：快慢指针，快慢指针相遇之后，慢指针回到头，快慢指针步调一致一起移动，相遇点即为入环点 。

![](../.gitbook/assets/56.png)

$$
\begin{gather*}
2⋅distance(slowptr) =distance(quickptr) \\
2(F+a) = F+a+b+a \\
F = b
\end{gather*}
$$

```cpp
ListNode *detectCycle(ListNode *head) {
    if(!head || !head->next)
        return NULL;
    ListNode *quickptr=head->next, *slowptr=head;
    while(quickptr && quickptr->next){
        if(quickptr == slowptr){
            quickptr = head;
            slowptr = slowptr->next;
            break;
        }
        quickptr = quickptr->next->next;
        slowptr = slowptr->next;
    }
    if(quickptr == head){
        while(quickptr != slowptr){
            quickptr = quickptr->next;
            slowptr = slowptr->next;
        }
        return quickptr;
    }
    return NULL;
}
```

### [palindrome-linked-list](https://leetcode-cn.com/problems/palindrome-linked-list/)

判断一个链表是否为回文链表。用 $$O(n)$$ 时间复杂度和 $$O(1)$$ 空间复杂度解决。

```cpp
bool isPalindrome(ListNode* head) {
    if(!head || !head->next)
        return true;
    ListNode *quickptr=head->next, *slowptr=head;
    while(quickptr && quickptr->next){
        quickptr = quickptr->next->next;
        slowptr = slowptr->next;
    }
    ListNode *rightHead = slowptr->next;
    slowptr->next = NULL;
    rightHead = reverseList(rightHead);
    // 合并
    ListNode *leftHead = head;
    while(leftHead && rightHead){
        if(leftHead->val != rightHead->val){
            return false;
        }
        leftHead = leftHead->next;
        rightHead = rightHead->next;
    }
    return true;
}
// 在实际项目中，最好在函数返回前将原链表恢复，这个函数理论上来说不能改动原链表。
```

### [copy-list-with-random-pointer](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。要求返回这个链表的 **深拷贝**。

```cpp
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;
    Node(int _val) { val = _val; next = NULL; random = NULL;}
};
```

![](../.gitbook/assets/57.png)

> 这道题的难度在于随机指针的复制，首先想到的是利用`map`存储原链表和新链表的对应节点的指针。

```cpp
Node* copyRandomList(Node* head) {
    if(!head)
        return NULL;
    std::map<Node*, Node*> imap;
    Node *ptr = head;
    Node *newHead = new Node(0);
    Node *newptr = newHead;
    while(ptr){
        newptr->next = new Node(ptr->val);
        newptr = newptr->next;
        imap.insert(std::make_pair(ptr, newptr));
        ptr = ptr->next;
    }
    ptr = head;
    newptr = newHead->next;
    while(ptr){
        if(ptr->random)
            newptr->random = imap.find(ptr->random)->second;
        ptr = ptr->next;
        newptr = newptr->next;
    }
    return newHead->next;
}
```

> 方法二、回溯法，（见[回溯法](../algorithm-thinking/backtracking-algorithm.md)）
>
> 方法三、不需要额外空间的迭代

```cpp

```

