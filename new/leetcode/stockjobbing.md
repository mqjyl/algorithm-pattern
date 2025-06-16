# 股票买卖问题

膜拜东哥：[https://labuladong.gitbook.io/algo/dong-tai-gui-hua-xi-lie/tuan-mie-gu-piao-wen-ti](https://labuladong.gitbook.io/algo/dong-tai-gui-hua-xi-lie/tuan-mie-gu-piao-wen-ti)

[121.买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

[122.买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

[123.买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)

[188.买卖股票的最佳时机 I](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)

[309.最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

[714.买卖股票的最佳时机含手续费](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

该系列题目是动态规划的热点考题，采用状态机的方式做。这 6 道题目是有共性的，以第 4 道题目为例，因为这道题是一个最泛化的形式，其他的问题都是这个形式的简化，看下题目：

> 给定一个数组，它的第 `i` 个元素是一支给定的股票在第 `i` 天的价格。设计一个算法来计算你所能获取的最大利润。你最多可以完成 `k` 笔交易。
>
> 注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

第一题是只进行一次交易，相当于 `k = 1`；第二题是不限交易次数，相当于 `k = +infinity`（正无穷）；第三题是只进行 2 次交易，相当于 `k = 2`；剩下两道也是不限次数，但是加了交易「冷冻期」和「手续费」的额外条件，其实就是第二题的变种。

## 一、状态穷举框架

利用「状态」进行穷举。我们具体到每一天，看看总共有几种可能的「状态」，再找出每个「状态」对应的「选择」。我们要穷举所有「状态」，穷举的目的是根据对应的「选择」更新状态：

```cpp
for 状态1 in 状态1的所有取值：
    for 状态2 in 状态2的所有取值：
        for ...
            dp[状态1][状态2][...] = 择优(选择1，选择2...)
```

比如说这个问题，**每天都有三种「选择」**：买入、卖出、无操作，我们用 `buy, sell, rest` 表示这三种选择。但问题是，并不是每天都可以任意选择这三种选择的，因为 sell 必须在 buy 之后，buy 必须在 sell 之后。那么 rest 操作还应该分两种状态，一种是 buy 之后的 rest（持有了股票），一种是 sell 之后的 rest（没有持有股票）。而且别忘了，我们还有交易次数 `k` 的限制，就是说你 buy 还只能在 `k > 0` 的前提下操作。

**这个问题的「状态」有三个**，第一个是天数，第二个是允许交易的最大次数，第三个是当前的持有状态（即之前说的 rest 的状态，我们不妨用 1 表示持有，0 表示没有持有）。然后我们用一个三维数组就可以装下这几种状态的全部组合：

```cpp
dp[i][k][0 or 1]
0 <= i <= n-1, 1 <= k <= K
n 为天数，大 K 为最多交易数
此问题共 n × K × 2 种状态，全部穷举就能搞定。

for 0 <= i < n:
    for 1 <= k <= K:
        for s in {0, 1}:
            dp[i][k][s] = max(buy, sell, rest)
```

用自然语言描述出每一个状态的含义，比如说 `dp[3][2][1]` 的含义就是：今天是第三天，我现在手上持有着股票，至今最多进行 2 次交易。再比如 `dp[2][3][0]` 的含义：今天是第二天，我现在手上没有持有股票，至今最多进行 3 次交易。

我们想求的最终答案是 `dp[n - 1][K][0]`，即最后一天，最多允许 K 次交易，最多获得多少利润。读者可能问为什么不是 `dp[n - 1][K][1]`？因为 `[1]` 代表手上还持有股票，`[0]` 表示手上的股票已经卖出去了，很显然后者得到的利润一定大于前者。

## 二、状态转移框架

上面完成了「状态」的穷举，我们开始思考每种「状态」有哪些「选择」，应该如何更新「状态」。只看「持有状态」，可以画个状态转移图。

![](<../.gitbook/assets/2 (1).png>)

通过这个图可以很清楚地看到，每种状态（0 和 1）是如何转移而来的。根据这个图，我们来写一下状态转移方程：

```cpp
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
              max( 选择 rest    ,     选择 sell )

解释：今天我没有持有股票，有两种可能：
要么是我昨天就没有持有，然后今天选择 rest，所以我今天还是没有持有；
要么是我昨天持有股票，但是今天我 sell 了，所以我今天没有持有股票了。

dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
              max( 选择 rest    ,     选择 buy  )

解释：今天我持有着股票，有两种可能：
要么我昨天就持有着股票，然后今天选择 rest，所以我今天还持有着股票；
要么我昨天本没有持有，但今天我选择 buy，所以今天我就持有股票了。
```

如果 buy，就要从利润中减去 `prices[i]`，如果 sell，就要给利润增加 `prices[i]`。今天的最大利润就是这两种可能选择中较大的那个。而且注意 k 的限制，我们在选择 buy 的时候，把 k 减小了 1，很好理解吧，当然你也可以在 sell 的时候减 1，一样的。

现在，我们已经有了：状态转移方程。还差最后一点点，就是定义 base case，即最简单的情况：

```cpp
// i 从 0 开始
dp[-1][k][0] = 0
解释：因为 i 是从 0 开始的，所以 i = -1 意味着还没有开始，这时候的利润当然是 0 。
dp[-1][k][1] = -infinity
解释：还没开始的时候，是不可能持有股票的，用负无穷表示这种不可能。

// k 从 1 开始
dp[i][0][0] = 0
解释：因为 k 是从 1 开始的，所以 k = 0 意味着根本不允许交易，这时候利润当然是 0 。
dp[i][0][1] = -infinity
解释：不允许交易的情况下，是不可能持有股票的，用负无穷表示这种不可能。
```

把上面的状态转移方程总结一下：

```cpp
base case:
dp[-1][k][0] = dp[i][0][0] = 0
dp[-1][k][1] = dp[i][0][1] = -infinity

状态转移方程：
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
```

这个数组索引是 -1 怎么编程表示出来呢，负无穷怎么表示呢？这都是细节问题，有很多方法实现。

## 三、秒杀题目

### 第一题，k = 1

直接套状态转移方程：

```cpp
base case:
dp[-1][1][0] = dp[i][0][0] = 0
dp[-1][1][1] = dp[i][0][1] = -infinity

状态转移方程：
dp[i][1][0] = max(dp[i-1][1][0], dp[i-1][1][1] + prices[i])
dp[i][1][1] = max(dp[i-1][1][1], dp[i-1][0][0] - prices[i]) 
            = max(dp[i-1][1][1], -prices[i])
解释：k = 0 的 base case，所以 dp[i-1][0][0] = 0。
```

现在发现`k`都是`1`，不会改变，即`k`对状态转移已经没有影响了。可以进一步化简去掉所有 `k`：

```cpp
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], -prices[i])
```

对 `i` 的 base case 进行处理。可以直接写出代码：

```cpp
int maxProfit(vector<int>& prices) {
    int len = prices.size();
    vector<vector<int>> dp(len, vector<int>(2));

    for (int i = 0; i < len; i++) {
        if (i - 1 == -1) {
            dp[i][0] = 0;
            // 解释：
            //   dp[i][0]
            // = max(dp[-1][0], dp[-1][1] + prices[i])
            // = max(0, -infinity + prices[i]) = 0
            dp[i][1] = -prices[i];
            //解释：
            //   dp[i][1] 
            // = max(dp[-1][1], dp[-1][0] - prices[i])
            // = max(-infinity, 0 - prices[i])
            // = -prices[i]
            continue;
        }
        dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i]);
        dp[i][1] = max(dp[i-1][1], -prices[i]);
    }
    return len > 0 ? dp[len - 1][0] : 0;
}
```

第一题就解决了，但是这样处理 `base case` 很麻烦，而且注意一下状态转移方程，新状态只和相邻的一个状态有关，其实不用整个 `dp` 数组，只需要一个变量储存相邻的那个状态就足够了，这样可以把空间复杂度降到 $$O(1)$$ ：

```cpp
// k == 1
int maxProfit(std::vector<int>& prices){
    int len = prices.size();
    int dp_i_0 = 0, dp_i_1 = INT_MIN;  // i = -1
    for(int i = 0; i < len; i++){
        dp_i_0 = max(dp_i_0, dp_i_1 + prices[i]);
        dp_i_1 = max(dp_i_1, -prices[i]);
    }
    return dp_i_0;
}
```

### **第二题，k = +infinity**

如果 k 为正无穷，那么就可以认为 k 和 k - 1 是一样的。可以这样改写框架：

```cpp
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
            = max(dp[i-1][k][1], dp[i-1][k][0] - prices[i])
```

我们发现数组中的 k 已经不会改变了，也就是说不需要记录 k 这个状态了，进一步化简去掉所有 `k`：：

```cpp
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i])
```

直接翻译成代码：

```cpp
// k = +infinity
int maxProfit(vector<int>& prices) {
    int len = prices.size();
    int dp_i_0 = 0, dp_i_1 = INT_MIN;  // i = -1
    for(int i = 0; i < len; ++i){
        int temp = dp_i_0;
        dp_i_0 = max(dp_i_0, dp_i_1 + prices[i]);
        dp_i_1 = max(dp_i_1, temp - prices[i]);
    }
    return dp_i_0;
}
```

### **第三题，`k = +infinity with cooldown`**

每次 sell 之后要等一天才能继续交易。只要把这个特点融入上一题的状态转移方程即可：

```cpp
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-2][0] - prices[i])
解释：第 i 天选择 buy 的时候，要从 i-2 的状态转移，而不是 i-1 。
```

翻译成代码：

```cpp
// k = +infinity with cooldown
int maxProfit(vector<int>& prices) {
    int len = prices.size();
    int dp_i_0 = 0, dp_i_1 = INT_MIN;
    int dp_i_pre = 0;
    for(int i = 0; i < len; ++i){
        int temp = dp_i_0;
        dp_i_0 = max(dp_i_0, dp_i_1 + prices[i]);
        dp_i_1 = max(dp_i_1, dp_i_pre - prices[i]);
        dp_i_pre = temp;
    }
    return dp_i_0;
}
```

### **第四题，k = +infinity with fee**

每次交易要支付手续费 fee，只要把手续费从利润中减去即可。改写方程：

```cpp
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i] - fee)
解释：相当于买入股票的价格升高了。在第一个式子里减也是一样的，相当于卖出股票的价格减小了。
```

直接翻译成代码：

```cpp
// k = +infinity with fee
int maxProfit(vector<int>& prices, int fee) {
    int len = prices.size();
    int dp_i_0 = 0, dp_i_1 = INT_MIN;  // i = -1
    for(int i = 0; i < len; ++i){
        int temp = dp_i_0;
        dp_i_0 = max(dp_i_0, dp_i_1 + prices[i]);
        dp_i_1 = max(dp_i_1, temp - prices[i] - fee);
    }
    return dp_i_0;
}
```

### **第五题，k = 2**

k = 2 和前面题目的情况稍微不同，因为上面的情况都和 k 的关系不太大。要么 k 是正无穷，状态转移和 k 没关系了；要么 k = 1，跟 k = 0 这个 base case 挨得近，最后也没有存在感。这道题 k = 2 和后面要讲的 k 是任意正整数的情况中，对 k 的处理就凸显出来了。这道题由于没有消掉 k 的影响，所以必须要对 k 进行穷举：

```cpp
int maxProfit(vector<int>& prices) {
    int len = prices.size();
    int dp_0[3], dp_1[3];
    // base case
    for(int k = 0; k < 3; ++k){
        dp_0[k] = 0, dp_1[k] = INT_MIN;
    }
    for(int i = 0; i < len; ++i){
        for(int j = 1; j <= 2; ++j){
            dp_0[j] = max(dp_0[j], dp_1[j] + prices[i]);
            dp_1[j] = max(dp_1[j], dp_0[j - 1] - prices[i]);
        }
    }
    // 穷举了 n × max_k × 2 个状态，正确。
    return dp_0[2];
}
```

这里 k 取值范围比较小，所以可以不用 for 循环，直接把 k = 1 和 2 的情况全部列举出来也可以：

```cpp
dp[i][2][0] = max(dp[i-1][2][0], dp[i-1][2][1] + prices[i])
dp[i][2][1] = max(dp[i-1][2][1], dp[i-1][1][0] - prices[i])
dp[i][1][0] = max(dp[i-1][1][0], dp[i-1][1][1] + prices[i])
dp[i][1][1] = max(dp[i-1][1][1], -prices[i])

int maxProfit(vector<int>& prices) {
    int dp_i10 = 0, dp_i11 = INT_MIN;
    int dp_i20 = 0, dp_i21 = INT_MIN;

    for(int price : prices){
        dp_i10 = max(dp_i10, dp_i11 + price);
        dp_i11 = max(dp_i11, -price);
        dp_i20 = max(dp_i20, dp_i21 + price);
        dp_i21 = max(dp_i21, dp_i10 - price);
    }
    return dp_i20;
}
```

有状态转移方程和含义明确的变量名指导，很容易看懂。

### **第六题，k = any integer**

有了上一题 k = 2 的铺垫，这题应该和上一题的第一个解法没啥区别。但是出现了一个超内存的错误，原来是传入的 k 值会非常大，`dp` 数组太大了。现在想想，交易次数 k 最多有多大呢？一次交易由买入和卖出构成，至少需要两天。所以说有效的限制 k 应该不超过 n/2，如果超过，就没有约束作用了，相当于 k = +infinity。这种情况是之前解决过的。直接把之前的代码重用：

```cpp
int maxProfit_infinity(std::vector<int>& prices){
    int len = prices.size();
    int dp_i_0 = 0, dp_i_1 = INT_MIN;  // i = -1
    for(int i = 0; i < len; ++i){
        int temp = dp_i_0;
        dp_i_0 = max(dp_i_0, dp_i_1 + prices[i]);
        dp_i_1 = max(dp_i_1, temp - prices[i]);
    }
    return dp_i_0;
}

int maxProfit(int k, vector<int>& prices) {
    int len = prices.size();
    if(k > len / 2)
        return maxProfit_infinity(prices);
    int dp_0[k + 1], dp_1[k + 1];
    // base case
    for(int t = 0; t <= k; ++t){
        dp_0[t] = 0, dp_1[t] = INT_MIN;
    }
    for(int i = 0; i < len; ++i){
        for(int j = 1; j <= k; ++j){
            dp_0[j] = max(dp_0[j], dp_1[j] + prices[i]);
            dp_1[j] = max(dp_1[j], dp_0[j - 1] - prices[i]);
        }
    }
    return dp_0[k];
}
```

### **总结**

**动态规划**关键就在于列举出所有可能的「状态」，然后想想怎么穷举更新这些「状态」。一般用一个多维 `dp` 数组储存这些状态，从 base case 开始向后推进，推进到最后的状态，就是我们想要的答案。具体到股票买卖问题，我们发现了三个状态，使用了一个三维数组，然后是确定每个状态下的选择，总共有三种选择，所以动态规划无非还是穷举（状态） + （选择）更新。
