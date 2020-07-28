# 滑动窗口思想

双指针有三兄弟：快慢指针（链表）、左右指针（数组，串）、滑动窗口（链表，数组，串）。滑动窗口核心点是维护一个窗口集，根据窗口集来进行处理。

滑动窗口的应用场景有几个特点：

* 需要输出或比较的结果在原数据结构中是连续排列的；
* 每次窗口滑动时，只需观察窗口两端元素的变化，无论窗口多长，每次只操作两个头尾元素，当用到的窗口比较长时，可以显著减少操作次数；
* 窗口内元素的整体性比较强，窗口滑动可以只通过操作头尾两个位置的变化实现，但对比结果时往往要用到窗口中所有元素。

核心步骤：

* right 右移
* 收缩
* left 右移
* 求结果

## ✏ 双指针题目

### \*\*\*\*[**Container With Most Water**](https://leetcode-cn.com/problems/container-with-most-water/)\*\*\*\*

给 ****`n` 个非负整数 $$a_1$$ ，$$a_2$$，...，$$a_n$$，每个数代表坐标中的一个点  $$(i,a_i)$$ 。在坐标内画 `n` 条垂直线，垂直线 `i` 的两个端点分别为 $$(i,a_i)$$  和 $$(i,0)$$ 。找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

说明：不能倾斜容器，且 n 的值至少为 2。

> **算法流程：** 设置双指针 start，stop 分别位于容器壁两端，根据规则移动指针，并且更新面积最大值 `result`，直到 `start == stop` 时返回 `result`。
>
>  **指针移动规则：**设每一状态下水槽面积为 $$S(start,stop),(0<=start<stop<=n)$$ ，由于水槽的实际高度由两板中的短板决定，则可得面积公式 $$S(start,stop) = min(h[start], h[stop]) × (stop - start)$$。 
>
> 在每一个状态下，无论长板或短板收窄 1 格，都会导致水槽 底边宽度 −1： 
>
> * 若向内移动短板，水槽的短板 $$min(h[start], h[stop])$$ 可能变大，因此水槽面积 $$S(start,stop)$$ 可能增大。 
> * 若向内移动长板，水槽的短板 $$min(h[start], h[stop])$$ 不变或变小，下个水槽的面积一定小于当前水槽面积。
>
> 因此，向内收窄短板可以获取面积最大值。
>
> 换个角度理解： 若不指定移动规则，所有移动出现的 $$S(start,stop)$$ 的状态数为 $$C(n,2)$$ ，即暴力枚举出所有状态。 在状态 $$S(start,stop)$$ 下向内移动短板至 $$S(start + 1,stop)$$ （假设 $$height[start] < height[stop]$$ ），则相当于消去了 $$\{ S(start,stop - 1),  S(start,stop - 2), \ldots, S(start,start + 1) \}$$ 状态集合。而所有消去状态的面积一定 $$<=S(start,stop)$$ ： 
>
> * 短板高度：相比 $$S(start,stop)$$ 相同或更短（ $$<=height[start]$$ ）； 
> * 底边宽度：相比 $$S(start,stop)$$ 更短。 
>
> 因此所有消去的状态的面积都 $$< S(start,stop)$$ 。通俗的讲，我们每次向内移动短板，所有的消去状态都不会导致丢失面积最大值 。

```cpp
int maxArea(vector<int>& height) {
    int start = 0;
    int stop = height.size() - 1;
    int result = min(height[start], height[stop]) * (stop - start);
    while(start < stop){
        height[start] > height[stop] ? stop-- : start++;
        result = max(result, min(height[start], height[stop]) * (stop - start));
    }
    return result;
}
```

## ✏ 滑动窗口题目

### 模板 🍇 

```cpp
/* 滑动窗口算法框架 */
void slidingWindow(string s, string t) {
    unordered_map<char, int> need, window;
    for(char c : t) need[c]++;
    
    int left = 0, right = 0;
    int valid = 0; 
    while(right < s.size()) {
        // c 是将移入窗口的字符
        char c = s[right];
        // 右移窗口
        right++;
        // 进行窗口内数据的一系列更新
        ...

        /*** debug 输出的位置 ***/
        printf("window: [%d, %d)\n", left, right);
        /********************/
        
        // 判断左侧窗口是否要收缩
        while(window needs shrink) {
            // 更新结果
            ...
            // d 是将移出窗口的字符
            char d = s[left];
            // 左移窗口
            left++;
            // 进行窗口内数据的一系列更新
            ...
        }
    }
}
```

说明：

1.  使用 `left` 和 `right` 变量初始化窗口的两端，注意区间 `[left, right)` 是左闭右开的，所以初始情况下窗口没有包含任何元素，`right`也要从`0`开始。
2. 其中 `valid` 变量表示窗口中满足 `need` 条件的字符个数，如果 `valid` 和 `need.size` 的大小相同，则说明窗口已满足条件，已经完全覆盖了串 `T`。

套模板，只需要思考以下四个问题：

1. 当移动 `right` 扩大窗口，即加入字符时，应该更新哪些数据？
2. 什么条件下，窗口应该暂停扩大，开始移动 `left` 缩小窗口？
3. 当移动 `left` 缩小窗口，即移出字符时，应该更新哪些数据？
4. 我们要的结果应该在扩大窗口时还是缩小窗口时进行更新？

如果一个字符进入窗口，应该增加 `window` 计数器；如果一个字符将移出窗口的时候，应该减少 `window` 计数器； 当 `valid` 满足 `need` 时应该收缩窗口；应该在收缩窗口的时候更新最终结果。

### \*\*\*\*[**Minimum Size Subarray Sum**](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)\*\*\*\*

给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 `≥ s` 的长度最小的 连续 子数组，并返回其长度。如果不存在符合条件的子数组，返回 0。

 **进阶：**如果你已经完成了 __$$O(n)$$ 时间复杂度的解法, 请尝试 $$O(n log n)$$ 时间复杂度的解法。

> 一、滑动窗口：右端依次进入，当窗口内的和大于等于s的时候，左端开始移出直到和小于s，用此时的窗口长度+1去更新全局的最小窗口长度。

```cpp
int minSubArrayLen(int s, vector<int>& nums) {
    if(nums.empty())
        return 0;
    int start = 0, stop = 0;
    int sum = 0;
    int result = nums.size() + 1;
    int tmpLen = 0;
    while(stop < nums.size()){
        sum += nums[stop];
        tmpLen++;
        if(sum >= s){
            while(sum >= s){
                sum -= nums[start++];
                tmpLen--;
            }
            result = min(result, tmpLen + 1);
        }
        stop++;
    }
    return result == nums.size() + 1 ? 0 : result;
}
```

> 二、二分查找：额外创建一个数组 $$\text{sums}$$ 用于存储数组 $$\text{sums}$$ 的前缀和，其中 $$\text{sums}[i]$$ 表示从 $$\text{nums}[0]$$ 到 $$\text{nums}[i - 1]$$ 的元素和。得到前缀和之后，对于每个开始下标 $$i$$ ，可通过二分查找得到大于或等于 $$i$$ 的最小下标 `bound`，使得 $$\text{sums}[\textit{bound}]-\text{sums}[i-1] \ge s$$ ，并更新子数组的最小长度（此时子数组的长度是 $$\textit{bound}-(i-1)$$ \)。
>
>  **因为这道题保证了数组中每个元素都为正，所以前缀和一定是递增的，这一点保证了二分的正确性。**

```cpp
int lower_bound(std::vector<int>& nums, int target){
    int left = 0, right = nums.size() - 1;
    int mid = -1;
    while(left < right){
        mid = (left + right) / 2;
        if(nums[mid] < target)
            left = mid + 1;
        else
            right = mid;
    }
    return nums[left] >= target ? left : -1;
}
int minSubArrayLen(int s, vector<int>& nums) {
    int len = nums.size();
    if(len == 0)
        return 0;
    int result = len + 1;
    std::vector<int> sums(len + 1, 0);
    for(int i = 1;i <= len;i++){
        sums[i] = sums[i - 1] + nums[i - 1];
    }
    for(int i = 1;i <= len;i++){
        int target = sums[i - 1] + s;
        // auto bound = std::lower_bound(sums.begin(), sums.end(), target);
        int bound = lower_bound(sums, target);
        //if(bound != sums.end()){
        //    result = std::min(result, static_cast<int>(bound - sums.begin()) - (i - 1));
        //}
        if(bound != -1){
            result = std::min(result, bound - (i - 1));
        }
    }
    return result == len + 1 ? 0 : result;
}
```

在很多语言中，都有现成的库和函数来为我们实现这里二分查找大于等于某个数的第一个位置的功能，比如 C++ 的 `lower_bound`，Java 中的 `Arrays.binarySearch`，C\# 中的 `Array.BinarySearch`，Python 中的 `bisect.bisect_left`。

### [minimum-window-substring](https://leetcode-cn.com/problems/minimum-window-substring/)

给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字符的最小子串。

**说明：**

* 如果 S 中不存这样的子串，则返回空字符串 `""`。
* 如果 S 中存在这样的子串，保证它是唯一的答案。
* 此题的测试用例中有 `s="a",t="aa",答案是""`。说明不能忽略重复。

```cpp
string minWindow(string s, string t) {
    if(s.empty() || t.empty())
        return "";
    std::unordered_map<char, int> need, window;
    for(char c : t)
        need[c]++;
    int left = 0, right = 0;
    int valid = 0;
    int len = s.size() + 1, start = 0;
    while(right < s.size()){
        char c = s[right];
        right++;
        if(need.count(c) > 0){
            window[c]++;
            if(need[c] == window[c])
                valid++;
        }
        while(valid == need.size()){
            if(len > right - left){
                len = right - left;
                start = left;
            }
            char d = s[left];
            left++;
            if(need.count(d) > 0){
                if(need[d] == window[d])
                    valid--;
                window[d]--;
            }
        }
    }
    return len == s.size() + 1 ? "" : s.substr(start, len);
}
```

> 复杂度分析：
>
> 时间复杂度：最坏情况下左右指针对 $$s$$ 的每个元素各遍历一遍，哈希表中对 $$s$$ 中的每个元素各插入、删除一次，对 $$t$$ 中的元素各插入一次，则渐进时间复杂度为 $$O( |s| + |t|)$$ 。

> 空间复杂度：用了两张哈希表作为辅助空间，每张哈希表最多不会存放超过`t`的字符集大小的键值对，我们设字符集大小为 $$C$$ ，则渐进空间复杂度为 $$O(C)$$ 。

### [permutation-in-string](https://leetcode-cn.com/problems/permutation-in-string/)

给定两个字符串 **s1** 和 **s2**，写一个函数来判断 **s2** 是否包含 **s1** 的排列。换句话说，第一个字符串的排列之一是第二个字符串的子串。

**注意：**

1. 输入的字符串只包含小写字母
2. 两个字符串的长度都在 \[1, 10,000\] 之间

```cpp
bool checkInclusion(string s1, string s2) {
    std::unordered_map<char, int> need, window;
    for(char c : s1)
        need[c]++;
    int left = 0, right = 0;
    int valid = 0;
    while(right < s2.size()){
        char c = s2[right];
        right++;
        if(need.count(c)){
            window[c]++;
            if(need[c] == window[c])
                valid++;
        }
        while(right - left >= s1.size()){
            if(valid == need.size())
                return true;
            char d = s2[left];
            left++;
            if(need.count(d)){
                if(need[d] == window[d])
                    valid--;
                window[d]--;
            }
        }
    }
    return false;
}
```

对于这道题的解法代码，基本上和最小覆盖子串一模一样，只需要改变两个地方：

1. 本题移动 left 缩小窗口的时机是窗口大小大于 `t.size()` 时，因为排列嘛，显然长度应该是一样的。
2. 当发现 `valid == need.size()` 时，就说明窗口中就是一个合法的排列，所以立即返回 true。

至于如何处理窗口的扩大和缩小，和最小覆盖子串完全相同。

### [find-all-anagrams-in-a-string](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

给定一个字符串 s 和一个非空字符串 p，找到 s 中所有是 p 的字母异位词的子串，返回这些子串的起始索引。字符串只包含小写英文字母，并且字符串 s 和 p 的长度都不超过 20100。

说明：字母异位词指字母相同，但排列不同的字符串。 不考虑答案输出的顺序。

```cpp
vector<int> findAnagrams(string s, string p) {
    std::unordered_map<char, int> need, window;
    std::vector<int> result;
    for(char c : p)
        need[c]++;
    int left = 0, right = 0;
    int valid = 0;
    while(right < s.size()){
        char c = s[right];
        right++;
        if(need.count(c)){
            window[c]++;
            if(need[c] == window[c])
                valid++;
        }
        while(right - left >= p.size()){
            if(valid == need.size())
                result.push_back(left);
            char d = s[left];
            left++;
            if(need.count(d)){
                if(need[d] == window[d])
                    valid--;
                window[d]--;
            }
        }
    }
    return result;
}
```

### [longest-substring-without-repeating-characters](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

 给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

```cpp
int lengthOfLongestSubstring(string s) {
    int result = 0;
    int len = s.size();
    if(len == 0 || len == 1)
        return  len;
    int start = 0, current = 1;
    while(current < len){
        for(int i = current - 1; i >= start; i--){
            if(s[i] == s[current]){
                start = i + 1;
                break;
            }
        }
        result = result < (current - start + 1) ? (current - start + 1) : result;
        current++;
    }

    return result;
}
```

### \*\*\*\*[**Max Consecutive Ones III**](https://leetcode-cn.com/problems/max-consecutive-ones-iii/)\*\*\*\*

给定一个由若干 `0` 和 `1` 组成的数组 `A`，我们最多可以将 `K` 个值从 0 变成 1 （`0 <= K <= A.length`）。返回仅包含 1 的最长（连续）子数组的长度。

```cpp
int lengthOfLongestSubstring(string s) {
    int result = 0;
    int len = s.size();
    if(len == 0 || len == 1)
        return  len;
    int start = 0, current = 1;
    while(current < len){
        for(int i = current - 1; i >= start; i--){
            if(s[i] == s[current]){
                start = i + 1;
                break;
            }
        }
        result = result < (current - start + 1) ? (current - start + 1) : result;
        current++;
    }

    return result;
}
```



### 

