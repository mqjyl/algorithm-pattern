# 打家劫舍问题

## :pencil2: 1、**打家劫舍【**[**链接**](https://leetcode-cn.com/problems/house-robber/)**】**

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

&#x20;**解决动态规划问题就是找「状态」和「选择」：** **房子的索引就是状态，抢和不抢就是选择。**

> 正向思考，先状态，后选择：`dp[i] = x` 表示抢劫完第`i`间房屋，最多能抢到的钱为`x`。
>
> 则对于第`i`间房屋的选择有：1、第`i - 1`间房屋没抢，则该房屋可以抢，此时有`dp[i] = dp[i - 2] + nums[i]`；2、第`i - 1`间房屋抢了，则该房屋不能抢，则有`dp[i] = dp[i - 1]`；其实还有第三种选择，第`i - 1`间房屋没抢，则该房屋可以抢，此时有`dp[i - 1] + nums[i]`；但是该情况下`dp[i - 1]<=dp[i - 2]`，因此只有两种情况，二者取较大者。
>
> 空间优化：当前状态只有`i-1`和`i-2`两种状态有关，则用两个变量记录状态即可。

{% tabs %}
{% tab title="正向思考" %}
```cpp
int rob(vector<int>& nums) {
    if(nums.empty())
        return 0;
    int len = nums.size();
    int dp[len + 1];
    dp[0] = 0;
    dp[1] = nums[0];
    for(int i = 1; i < len; ++i){
        dp[i + 1] = max(dp[i - 1] + nums[i], dp[i]);
    }
    return dp[len];
}
```
{% endtab %}

{% tab title="空间优化" %}
```cpp
int rob(vector<int>& nums) {
    if(nums.empty())
        return 0;
    int dp_0 = 0, dp_1 = nums[0];
    for(int i = 1; i < nums.size(); ++i){
        int tmp = dp_1;
        dp_1 = max(dp_0 + nums[i], dp_1);
        dp_0 = tmp;
    }
    return dp_1;
}
```
{% endtab %}
{% endtabs %}

> 反向思考，先选择，后状态：`dp[i] = x` 表示从第 `i` 间房子开始抢劫，最多能抢到的钱为 `x`。
>
> 则对于第`i`间房屋的选择有：1、第`i`间房屋不抢，则去抢下一间房屋；2、抢第`i`间房屋，则要跳过下一间房屋；同样还有第三种选择，第`i`间房屋不抢，则跳过下一间房屋，这种情况包括在第`i+1`间房屋的选择中，因此只有两种情况，二者取较大者。

{% tabs %}
{% tab title="反向思考，超时" %}
```cpp
int dp(vector<int> &nums, int start){
    if(start >= nums.size()){
        return 0;
    }
    return max(dp(nums, start + 1), /*不抢，去下家*/
               nums[start] + dp(nums, start + 2) /*抢，跳过下家*/
               );
}
int rob(vector<int>& nums) {
    return dp(nums, 0);
}
```
{% endtab %}

{% tab title="备忘录优化" %}
```cpp
int dp(vector<int> &nums, int start, vector<int> &memo){
    if(start >= nums.size())
        return 0;
    if(memo[start] != -1)
        return memo[start];
    int res = max(dp(nums, start + 1, memo), nums[start] 
                  + dp(nums, start + 2, memo));
    memo[start] = res;
    return res;
}
int rob(vector<int>& nums) {
    vector<int> memo(nums.size(), -1);
    return dp(nums, 0, memo);
}
```
{% endtab %}

{% tab title="自底向上" %}
```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
    int dp[n + 2];
    memset(dp, 0, sizeof dp);
    for (int i = n - 1; i >= 0; i--) {
        dp[i] = max(dp[i + 1], nums[i] + dp[i + 2]);
    }
    return dp[0];
}
```
{% endtab %}

{% tab title="空间优化" %}
```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
    int dp_1 = 0, dp_2 = 0;
    for (int i = n - 1; i >= 0; i--) {
        int tmp = dp_1;
        dp_1 = max(dp_1, nums[i] + dp_2);
        dp_2 = tmp;
    }
    return dp_1;
}
```
{% endtab %}
{% endtabs %}

## :pencil2: 2、打家劫舍II【[链接](https://leetcode-cn.com/problems/house-robber-ii/)】

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都围成一圈，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。

> 首尾房间不能同时被抢，那么只可能有三种不同情况：要么都不被抢；要么第一间房子被抢最后一间不抢；要么最后一间房子被抢第一间不抢。



## :pencil2: 3、打家劫舍III【[链接](https://leetcode-cn.com/problems/house-robber-iii/)】

在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

### 树结构的动态规划拓展

#### [**Binary Tree Cameras**](https://leetcode-cn.com/problems/binary-tree-cameras/)

####
