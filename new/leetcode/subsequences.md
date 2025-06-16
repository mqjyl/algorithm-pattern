# 子序列问题

## :pencil2: 1、连续子序列问题

### :pen\_fountain: 1.1、[**最长重复子数组**](https://leetcode-cn.com/problems/maximum-length-of-repeated-subarray/)

&#x20;给两个整数数组 `A` 和 `B` ，返回两个数组中公共的、长度最长的子数组的长度。

> 方法一：动态规划： `dp[i][j]` 表示长度为 i，末尾项为 `A[i-1]` 的子数组，长度为 j，末尾项为 `B[j-1]` 的子数组，二者的最大公共后缀子数组长度。如果 `A[i-1] != B[j-1]`，`dp[i][j] = 0` 如果 `A[i-1] == B[j-1]` ，`dp[i][j] = dp[i-1][j-1] + 1` 。
>
> base case：如果 `i==0 || j==0`，其中一个数组长度为 0，没有公共部分，`dp[i][j] = 0` 最长公共子数组以哪一项为末尾项都有可能，即每个 `dp[i][j]` 都可能是最大值。
>
> 方法二：滑动窗口：枚举 A 和 B 所有的对齐方式。对齐的方式有两类：第一类为 A 不变，B 的首元素与 A 中的某个元素对齐；第二类为 B 不变，A 的首元素与 B 中的某个元素对齐。对于每一种对齐方式，计算它们相对位置相同的重复子数组即可。时间复杂度： $$O((N + M) \times \min(N, M))$$ 。
>
> 方法三：如果数组 A 和 B 有一个长度为 k 的公共子数组，那么它们一定有长度为 $$j \le k$$ 的公共子数组。这样我们可以通过二分查找的方法找到最大的 k。而为了优化时间复杂度，在二分查找的每一步中，使用哈希的方法来判断数组 A 和 B 中是否存在相同特定长度的子数组。（搬运一下官网的讲解）

![](<../.gitbook/assets/image (18).png>)

{% tabs %}
{% tab title="动态规划" %}
```cpp
int findLength(vector<int>& A, vector<int>& B) {
    int ret = 0;
    int dp[B.size() + 1][A.size() + 1];
    memset(dp, 0, sizeof(dp));
    for(int i = B.size() - 1; i >= 0; i --){
        for(int j = A.size() - 1; j >= 0; j --){
            if(B[i] == A[j]){
                dp[i][j] = dp[i + 1][j + 1] + 1;
            }
            ret = std::max(ret, dp[i][j]);
        }
    }
    return ret;
}
int findLength(vector<int>& A, vector<int>& B) {
    int ret = 0;
    int dp[B.size() + 1][A.size() + 1];
    memset(dp, 0, sizeof(dp));
    for(int i = 1; i <= A.size(); i ++){
        for(int j = 1; j <= B.size(); j ++){
            if(B[i - 1] == A[j - 1]){
                // i 只与 i - 1 有关
                dp[i][j] = dp[i - 1][j - 1] + 1;
            }else{
                dp[i][j] = 0;
            }
            ret = std::max(ret, dp[i][j]);
        }
    }
    return ret;
}
// 优化到一维数组：第二层for循环我们使用的倒序的方式，这是因为dp数组后面的值会依赖
// 前面的值，而前面的值不依赖后面的值，所以后面的值先修改对前面的没影响，但前面的值
// 修改会对后面的值有影响，所以这里要使用倒序的方式。
int findLength(vector<int>& A, vector<int>& B) {
    int ret = 0;
    int dp[B.size() + 1];
    memset(dp, 0, sizeof(dp));
    for(int i = 0; i < A.size(); i ++){
        for(int j = B.size(); j > 0; j --){
            if(A[i] == B[j - 1]){
                dp[j] = dp[j - 1] + 1;
            }else{
                dp[j] = 0;
            }
            ret = std::max(ret, dp[j]);
        }
    }
    return ret;
}
```
{% endtab %}

{% tab title="滑动窗口" %}
```cpp
int findLength(vector<int>& A, vector<int>& B) {
    int ret = 0;
    int m = A.size(), n = B.size();
    for(int i = 0; i < m; i ++){
        int p = i, q = 0;
        int len = 0;
        while(p < m && q < n){
            len = A[p] == B[q] ? len + 1 : 0;
            ret = max(ret, len);
            p++, q++;
        }
    }
    for(int i = 0; i < n; i ++){
        int p = i, q = 0;
        int len = 0;
        while(p < n && q < m){
            len = B[p] == A[q] ? len + 1 : 0;
            ret = max(ret, len);
            p++, q++;
        }
    }
    return ret;
}
// 滑动窗口算法有个小优化，每次对齐后可以计算一下长度len是否已经小于或等于结果ret，
// 如果是，那我们就不用继续循环计算了，因为后面肯定没有更长的。
```
{% endtab %}

{% tab title="二分查找" %}
```cpp
int base = 113;
int mod = 1e9 + 7;
// 求pow(base, len - 1),注意溢出问题
long helper(int i){
    long res = 1;
    long ref = base;
    while(i != 0){
        //如果是奇数次幂，先抽一个底数出来，提前乘到结果中
        if(i % 2 != 0){
            res = (res * ref) % mod;
        }
        //底数平方，幂减半
        ref = (ref * ref) %mod;
        i /= 2;
    }
    return res;
}
// 检验是否存在长度为len的公共子数组
bool check(std::vector<int>& A, std::vector<int>& B, int len){
    // 算出A[0, len - 1]的哈希值
    long hashA = 0;
    for(int i = 0; i < len; i++)
        hashA = (hashA * base + A[i]) % mod;
    unordered_set<int> setA;
    setA.insert(hashA);
    // 根据公式递推后面对应长度的哈希值，并存入哈希表
    long ref =  helper(len - 1) ;
    for(int i = 0; i < A.size() - len; i ++){
        // +mod 是为了保证 hashA 大于0
        hashA = ((hashA - A[i] * ref % mod + mod) % mod * base + A[i + len]) % mod;
        setA.insert(hashA);
    }
    // 同样的方式处理B数组
    long hashB = 0;
    for(int i = 0; i < len; i++)
        hashB = (hashB * base + B[i]) % mod;
    if(setA.count(hashB))
        return true;
    for(int i = 0; i < B.size() - len; i++){
        hashB = ((hashB - B[i] * ref % mod + mod) % mod * base + B[i + len]) % mod;
        if(setA.count(hashB))
            return true;
    }
    return false;
}
int findLength(vector<int>& A, vector<int>& B) {
    // [left, right]是所有可能不存在“对应长度的公共子数组”的集合
    // （0是一定存在的，所以left从1开始，min(A.size() , B.size()) + 1
    // 是一定不存在的，所以也需要包含在内）
    int left = 1;
    int right = min(A.size(), B.size()) + 1;
    while(left < right){
        int mid = left + (right - left) / 2;
        if(check(A, B, mid))
            left = mid + 1;
        else
            right = mid;
    }
    return left - 1;
}
```
{% endtab %}
{% endtabs %}

### :pen\_fountain: **1.2、**[**乘积最大子数组**](https://leetcode-cn.com/problems/maximum-product-subarray/)

&#x20;给你一个整数数组 `nums` ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

> 思路：用 $$f_{\max}(i)$$ 开表示以第 i 个元素结尾的乘积最大子数组的乘积。状态转移方程：
>
> $$f_{max}(i) = max\{ f_{max}(i - 1) \times a_i, a_i \}$$ 。这里需要 **根据正负性进行分类讨论，**&#x8003;虑当前位置如果是一个负数的话，那么我们希望以它前一个位置结尾的某个段的积也是个负数，这样就可以负负得正，并且我们希望这个积尽可能「负得更多」，即尽可能小。如果当前位置是一个正数的话，我们更希望以它前一个位置结尾的某个段的积也是个正数，并且希望它尽可能地大。于是这里我们可以再维护一个 $$f_{\min}(i)$$ ，它表示以第 i 个元素结尾的乘积最小子数组的乘积，那么我们可以得到这样的动态规划转移方程：
>
> $$f_{max}(i) = max \{ f_{max}(i - 1) \times a_i, f_{min}(i - 1) \times a_i, a_i \}$$&#x20;
>
> $$f_{min}(i) = min_ \{ f_{max}(i - 1) \times a_i, f_{min}(i - 1) \times a_i, a_i \}$$&#x20;
>
> 优化空间：由于第 i 个状态只和第 i - 1 个状态相关，根据「滚动数组」思想，我们可以只用两个变量来维护 i - 1 时刻的状态，一个维护 $$f_{max}$$ ，一个维护 $$f_{min}$$ 。

```cpp
int maxProduct(vector<int>& nums) {
    int fmax = nums[0], fmin = nums[0];
    int ret = nums[0], tmp = 0;
    for(int i = 1; i < nums.size(); i ++){
        tmp = fmax;
        fmax = max(fmax * nums[i], max(fmin * nums[i], nums[i]));
        fmin = min(tmp * nums[i], min(fmin * nums[i], nums[i]));
        ret = max(fmax, ret);
    }
    return ret;
}
```

## :pencil2: 2、不连续子序列问题

### :pen\_fountain: 2.1、[最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

最长递增子序列（`Longest Increasing Subsequence`，简写 `LIS`）：给定一个无序的整数数组，找到其中最长上升子序列的长度。

> 思路一：动态规划： `dp[i]` 表示以第 i 个数字结尾的最长上升子序列的长度，`nums[i]`必须被选取。则状态转移方程为：
>
> $$dp[i] = \text{max}(dp[j]) + 1, \text{其中} \, 0 \leq j < i \, \text{且} \, \textit{num}[j]<\textit{num}[i]$$&#x20;

> 思路二：二分查找：

{% tabs %}
{% tab title="动态规划" %}
```cpp
int lengthOfLIS(vector<int>& nums) {
    if(nums.empty())
        return 0;
    vector<int> dp(nums.size(), 1);
    int ret = 1;
    for(int i = 1; i < nums.size(); i ++){
        int j = i - 1;
        for(; j >= 0; j --){
            if(nums[i] > nums[j]){
                dp[i] = max(dp[i], dp[j] + 1);
            }
        }
        ret = max(ret, dp[i]);
    }
    return ret;
}
```
{% endtab %}

{% tab title="二分查找" %}
```
```
{% endtab %}
{% endtabs %}

### :pen\_fountain: 2.2、[**递增子序列**](https://leetcode-cn.com/problems/increasing-subsequences/)

给定一个整型数&#x7EC4;**，**&#x627E;到所有该数组的递增子序列，递增子序列的长度至少是2。

### :pen\_fountain: 2.3、[最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/) <a href="#activity-name" id="activity-name"></a>

&#x20;给定一个字符串 `s` ，找到其中最长的回文子序列，并返回该序列的长度。可以假设 `s` 的最大长度为 `1000` 。

### :pen\_fountain: 2.4、[最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)

最长公共子序列（Longest Common Subsequence，简称 LCS）： 给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长公共子序列的长度。

### :pen\_fountain: 2.5、[**最长连续序列**](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

进阶：设计并实现时间复杂度为 $$O(n)$$ 的解决方案吗？

## :pencil2: 3、循环连续子序列问题

### :pen\_fountain: 3.1、[彩色宝石项链](https://www.nowcoder.com/questionTerminal/321bf2986bde4d799735dc9b493e0065)
