# 面试算法题总结

## ✏ 1、删除链表中一个给定节点（快手一面）

核心：删除一个节点的前提是找到它的前一个节点。正常的操作是先找到target的前一个节点，通过前一个节点删除target，思维转换一下可以将target的下一个节点的值赋给target，通过target把它的下一个删除掉，这种方法不适合target为链表最后一个节点的情况，如果target为最后一个节点，则得从头开始找target的前一个节点，即倒数第二个节点。

{% tabs %}
{% tab title="O\(n\)" %}
```cpp
void delNode(ListNode *head, ListNode *target){
    if(head == NULL)
        return;
    if(head == target){
        head = head->next;
        delete target;
        return;
    }
    ListNode *ptr = head;
    while(ptr){
        if(ptr->next == target){
            ptr->next = target->next;
            delete target;
            return;
        }
        ptr->next;
    }
}
```
{% endtab %}

{% tab title="O\(1\)" %}
```cpp
void delNode(ListNode *head, ListNode *target){
    if(head == NULL)
        return;
    if(target->next != NULL){
        target->val = target->next->val;
        ListNode *tmp = target->next;
        target->next = target->next->next;
        delete tmp;
        return;
    }else{
        ListNode *ptr = head;
        while(ptr){
            if(ptr->next == target){
                ptr->next = target->next;
                delete target;
                return;
            }
            ptr->next;
        }
    }
}
```
{% endtab %}
{% endtabs %}

## ✏ 2、洗牌程序（滴滴一面）

洗牌算法是将原来的数组进行打散，使原数组的某个数在打散后的数组中的每个位置上等概率的出现，刚好可以解决该问题。

由抽牌、换牌和插牌衍生出三种洗牌算法，其中抽牌和换牌分别对应`Fisher-Yates Shuffle`和`Knuth-Durstenfeld Shhuffle`算法。

### 🖋 2.1、

