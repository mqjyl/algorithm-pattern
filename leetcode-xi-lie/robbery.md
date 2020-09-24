# 打家劫舍问题

## ✏ 1、**打家劫舍【**[**链接**](https://leetcode-cn.com/problems/house-robber/)**】**

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

 **解决动态规划问题就是找「状态」和「选择」：** **房子的索引就是状态，抢和不抢就是选择。**

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
> 则对于第`i`间房屋的选择有：1、第`i`间房屋不抢，则去抢下一间房屋；2、第`iiii 1`间房屋抢了，则该房屋不能抢，则有`dp[i] = dp[i - 1]`；其实还有第三种选择，第`i - 1`间房屋没抢，则该房屋可以抢，此时有`dp[i - 1] + nums[i]`；但是该情况下`dp[i - 1]<=dp[i - 2]`，因此只有两种情况，二者取较大者。

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

## ✏ 2、打家劫舍II【[链接](https://leetcode-cn.com/problems/house-robber-ii/)】

## ✏ 3、打家劫舍III【[链接](https://leetcode-cn.com/problems/house-robber-iii/)】

