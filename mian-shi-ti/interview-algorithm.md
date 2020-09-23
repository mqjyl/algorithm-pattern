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

### 🖋 2.1、`Fisher-Yates Shuffle`

最早提出这个洗牌方法的是 Ronald A. Fisher 和 Frank Yates，即 Fisher–Yates Shuffle，其基本思想就是从原始数组中随机取一个之前没取过的数字到新的数组中，具体如下：

1. 初始化原始数组和新数组，原始数组长度为n\(已知\)；
2. 从还没处理的数组（假如还剩k个）中，随机产生一个 $$[0, k)$$ 之间的数字p（假设数组从0开始）；
3. 从剩下的k个数中把第p个数取出；
4. 重复步骤2和3直到数字全部取完；
5. 从步骤3取出的数字序列便是一个打乱了的数列。

下面证明其随机性，即每个元素被放置在新数组中的第 $$i$$ 个位置是 $$1/n$$ （假设数组大小是n）。

$$
P=\frac{n - 1}{n}\times\frac{n - 2}{n - 1}\times \ldots \times \frac{n - i + 1}{n - i + 2}\times \frac{1}{n - i + 1}=\frac{1}{n}
$$

```cpp
void Fisher_Yates_Shuffle(vector<int>& arr,vector<int>& res)
{
    int len = arr.size();
    srand((unsigned)time(NULL));
    for (int i = 0; i < len; ++i)
    {
        int k = rand() % arr.size();
        res.push_back(arr[k]);
        arr.erase(arr.begin() + k);
    }
}
```

### 🖋 2.2、`Knuth-Durstenfeld Shuffle`

Knuth 和 Durstenfeld 在Fisher 等人的基础上对算法进行了改进，在原始数组上对数字进行交互，省去了额外 $$O(n)$$ 的空间。该算法的基本思想和 Fisher 类似，每次从未处理的数据中随机取出一个数字，然后把该数字放在数组的尾部，即数组尾部存放的是已经处理过的数字。

该算法是经典洗牌算法。它的证明如下：

* 对于`arr[i]`洗牌后在第`n-1`个位置的概率是 $$1/n$$ （第一次交换的随机数为 $$i$$ ） 
* 在`n-2`个位置概率是 $$[(n-1)/n]  [1/(n-1)] = 1/n$$，（第一次交换的随机数不为 $$i$$ ，第二次为`arr[i]`所在的位置（注意，若 $$i=n-1$$ ，第一交换`arr[n-1]`会被换到一个随机的位置）） 
* 在第`n-k`个位置的概率是 $$[(n-1)/n]\times [(n-2)/(n-1)] \times \ldots \times[(n-k+1)/(n-k+2)]\times [1/(n-k+1)] = 1/n$$（第一个随机数不要为 $$i$$ ，第二次不为`arr[i]`所在的位置（随着交换有可能会变）......第 $$n-k$$ 次为`arr[i]`所在的位置）。

```cpp
void Knuth_Durstenfeld_Shuffle(vector<int> &arr)
{
    int len = arr.size();
    srand((unsigned)time(NULL));
    for (int i = len - 1; i >= 0; --i)
    {
        int k = rand() % (i + 1);
        swap(arr[k], arr[i]);
    }
}
```

**间复杂度为** $$O(n)$$ ，**空间复杂度为** $$O(1)$$ ，**缺点必须知道数组长度** $$n$$ 。原始数组被修改了，这是一个原地打乱顺序的算法，算法时间复杂度也从Fisher算法的 $$O(n^2)$$ 提升到了 $$O(n)$$ 。由于是从后往前扫描，无法处理不知道长度或动态增长的数组。

### 🖋 2.3、`Inside-Out Algorithm`

Inside-Out Algorithm 算法的基本思思是从前向后扫描数据，把位置i的数据随机插入到前 $$i$$ 个（包括第i个）位置中（假设为 $$k$$ ），这个操作是在新数组中进行，然后把原始数据中位置 $$k$$ 的数字替换新数组位置 $$i$$ 的数字。其实效果相当于新数组中位置 $$k$$ 和位置 $$i$$ 的数字进行交互。

证明如下： 

原数组的第 $$i$$ 个元素（随机到的数）在新数组的前 $$i$$ 个位置的概率都是： $$(1/i)\times[i/(i+1)]  [(i+1)/(i+2)] \ldots [(n-1)/n] = 1/n$$ ，（即第 $$i$$ 次刚好随机放到了该位置，在后面的 $$n-i$$ 次选择中该数字不被选中）。 

原数组的第 $$i $$ 个元素（随机到的数）在新数组的 $$i+1$$ （包括 $$i + 1$$ ）以后的位置（假设是第k个位置）的概率是： $$(1/k)\times [k/(k+1)]  [(k+1)/(k+2)] \times [(n-1)/n] = 1/n$$ （即第 $$k$$ 次刚好随机放到了该位置，在后面的 $$n-k$$ 次选择中该数字不被选中）。

```cpp
void Inside_Out_Shuffle(const vector<int>& arr, vector<int>& res)
{
    res.assign(arr.size(), 0);
    copy(arr.begin(), arr.end(), res.begin());
    srand((unsigned)time(NULL));
    for (int i = 0; i < arr.size(); ++i)
    {
        int k = rand() % (i + 1);
        swap(res[k], res[i]);
    }
}
```

## ✏ 3、任务调度（旷视面试）

有一个任务队列，共有n个任务和k个处理机，当任务到来时进入任务队列，每次从队列头部取任务给编号最小的一个空闲的处理机执行，每个任务都有自己的处理时间和到达时间，求每个任务对应的处理机的编号。

