# 背包问题

有 $$n$$ 种物品，物品 $$j$$ 的重量和价值分别为 $$w_j$$ 和 $$v_j$$ ，如果背包的最大容量限制是 $$m$$ ，怎么样选择放入背包的物品以使得背包的总价值最大？

组合优化问题，设 $$x_j$$ 表示装入背包的第 $$j$$ 个物品的数量，解可以表示为 $$<x_1,x_2,\ldots,x_n>$$ 。那么目标函数和约束条件是：

![](../.gitbook/assets/image%20%2863%29.png)

**如果组合优化问题的目标函数和约束条件都是线性函数，称为线性规划。如果线性规划问题的变量** $$x_j$$ **都是非负整数，则称为整数规划问题。背包问题就是整数规划问题。限制所有的** $$x_j = 0 or x_j = 1$$ **时称为0-1背包。**

## ✏ 1、0-1背包【[链接](https://www.nowcoder.com/questionTerminal/708f0442863a46279cce582c4f508658?orderByHotValue=1&page=1&onlyReference=false)】

有`n`件物品和容量为`m`的背包，给出每件物品的重量 $$w_i$$ 以及价值 $$v_i$$ ，求解让装入背包的物品重量不超过背包容量，且价值最大 。**特点：**每个物品只有一件供你选择放还是不放。

> 二维解法：设 $$f[i][j]$$ 表示前 $$i$$ 件物品 总重量不超过 $$j$$ 的最大价值，可得出状态转移方程：
>
> $$f[i][j]=max\{f[i-1][j-w[i]]+v[i],\ f[i-1][j]\}$$ 
>
> 一维解法：设 $$f[j]$$ 表示重量不超过 $$j$$ 公斤的最大价值，可得出状态转移方程：
>
> $$f[j]=max\{f[j],\ f[j−w[i]]+v[i]\}$$

![](../.gitbook/assets/image%20%2860%29.png)

{% tabs %}
{% tab title="二维解法" %}
```cpp
int maxValue(std::vector<int>& values, std::vector<int>& weight, int n, int m){
    int dp[n + 1][m + 1];
    memset(dp, 0, sizeof(dp));
    for(int i = 1; i <= n; ++i){
        for(int j = 1; j <= m; ++j){
            if(weight[i] <= j)
                dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + values[i]);
            else
                dp[i][j] = dp[i - 1][j];
        }
    }
    return dp[n][m];
}
```
{% endtab %}

{% tab title="一维解法" %}
```cpp
int maxValue(std::vector<int>& values, std::vector<int>& weight, int n, int m){
    int dp[m + 1];
    memset(dp, 0, sizeof(dp));
    for(int i = 1; i <= n; ++i){
        for(int j = m; j >= 1; --j){
            if(weight[i] <= j)
                dp[j] = max(dp[j], dp[j - weight[i]] + values[i]);
        }
    }
    return dp[m];
}
```
{% endtab %}
{% endtabs %}

### \*\*\*\*[**Partition Equal Subset Sum**](https://leetcode-cn.com/problems/partition-equal-subset-sum/)\*\*\*\*

## ✏ 2、完全背包【[链接](https://www.acwing.com/problem/content/3/)】

有 $$n$$ 件物品和容量为 $$m$$ 的背包，给出每件物品的重量 $$w_i$$ 以及价值 $$v_i$$ ，求解让装入背包的物品重量不超过背包容量，且价值最大 。**特点：**每个物品可以**无限选用**。

> 一维解法： 设 $$f[j]$$ 表示重量不超过 $$j$$ 公斤的最大价值，可得出状态转移方程：
>
> $$f[j]=max\{f[j],\ f[j−w[i]]+v[i]\}$$

![](../.gitbook/assets/image%20%2861%29.png)

```cpp
int maxValue(std::vector<int>& values, std::vector<int>& weight, int n, int m){
    int dp[m + 1];
    memset(dp, 0, sizeof(dp));
    for(int i = 1; i <= n; ++i){
        for(int j = weight[i]; j <= m; ++j){
            dp[j] = max(dp[j], dp[j - weight[i]] + values[i]);
        }
    }
    return dp[m];
}
```

### \*\*\*\*[**Coin Change**](https://leetcode-cn.com/problems/coin-change/)\*\*\*\*

### \*\*\*\*[**Coin Change 2**](https://leetcode-cn.com/problems/coin-change-2/)\*\*\*\*

## ✏ 3、多重背包**【**[**链接**](https://www.acwing.com/problem/content/4/)**】**（京东笔试）

有 $$n$$ 件物品和容量为 $$m$$ 的背包。给出每件物品的数量 $$s_i$$ 、重量 $$w_i$$ 以及价值 $$v_i$$ ，求解将哪些物品装入背包，可使物品体积总和不超过背包容量，且价值总和最大。**特点：** 每个物品都有了**一定的数量**。

> 二维解法：把 $$s_i$$ 个物品逐个拆分，则得到 $$\sum s_i$$ 个物品，原问题转化为01背包问题，时间复杂度为 $$O(m\times \sum s_i)$$ 。也可以在转移得过程中枚举 $$k$$ ，代表第 $$i$$ 个物品选取的数量，则转移方程为：
>
> $$f[i][j]=\mathop{max}\limits_{0\le k \le s[i]}\{f[i - 1][j - k\times v[i]] + k\times w[i]\}$$

> 一维解法：设 $$f[j]$$ 表示重量不超过 $$j$$ 公斤的最大价值，可得出状态转移方程：
>
> $$f[j] = \mathop{max}\limits_{0\le k \le s[i]}\{f[j−k\times w[i]]+k\times v[i]\}$$

```cpp
int maxValue(std::vector<int>& values, std::vector<int>& weight, 
             std::vector<int>& count, int n, int m){
    int dp[m + 1];
    memset(dp, 0, sizeof(dp));
    for(int i = 1; i <= n; ++i){
        for(int j = m; j >= weight[i]; --j){
            for(int k = 0; k <= count[i]; k++){
                if(j - k * weight[i] < 0)
                    break;
                dp[j] = max(dp[j], dp[j - k * weight[i]] + k * values[i]);
            }
        }
    }
    return dp[m];
}
```

### 优化：

1、二进制优化

将第 $$i$$ 种物品分成若干件物品，可以有 $$(v_i,w_i),(v_i\times 2,w_i\times 2),(v_i\times 4,w_i\times 4)\ldots$$ 等等，每件物品有一个系数，分别为 $$1,2,4,\ldots,2^{k - 1},n-2^k+1$$ ， $$k$$ 是满足的最大整数。例如， $$n$$ 为13，拆分成 $$1,2,4,6$$ 四件物品，根据二进制的性质， $$0\ldots 13$$ 都可以由这四件物品组合得到。于是， $$n$$ 件物品，就拆分成至多 $$logn$$ 件物品。再转化为01背包问题求解，复杂度降至 $$O(m\times \sum logn)$$ 。

```cpp
int maxValue(std::vector<int>& values, std::vector<int>& weight, 
             std::vector<int>& count, int n, int m){
    vector<pair<int, int> > lis;
    lis.push_back({0, 0});
    int idx, c;
    for(int i = 1; i <= n; ++i){
        c = 1;
        while(count[i] - c > 0){
            count[i] -= c;
            lis.push_back({c * values[i], c * weight[i]});
            c *= 2;
        }
        lis.push_back({count[i] * values[i], count[i] * weight[i]});
    }
    int dp[m + 1];
    memset(dp, 0, sizeof(dp));
    for(int i = 1; i <= lis.size(); ++i){
        for(int j = m; j >= lis[i].second; --j){
            dp[j] = max(dp[j], dp[j - lis[i].second] + lis[i].first);
        }
    }
    return dp[m];
}
```

2、单调队列优化

将背包的容量按照的剩余分成组：

![](../.gitbook/assets/10.png)

仔细观察转移方程，会发现真正的转移只会发生在同一组内，不同的组是不可能转移的可以改一下上面的转移方程：

$$f[b+x\times v[i]] = \mathop{max}\limits_{0\le k \le s[i]}\{f[b+(x-k)\times v[i]]+k\times w[i]\}$$ 

令 $$t = x - k$$ ，状态转移方程变成：

$$f[b+x\times v[i]] = \mathop{max}\limits_{x-s[i]\le t \le x}\{f[b+t\times v[i]]-t\times w[i]\} + x\times w[i]$$ 

这里的转移实际上一对 $$(b,x)$$ 唯一对应一个 $$j$$ 。我们固定 $$b$$ ，那么对于 $$x$$ 的决策 $$t$$ 的集合正好是一个滑动区间，而 $$max$$ 里面是一个和 $$x$$ 无关的式子，所以可以用单调队列来维护。具体算法如下：

* 枚举一个 $$i$$ 和 $$b(0\le b\le v[i])$$ ；
* 从小到大枚举 $$x$$ ，并且用单调队列来维护 $$f[b+t\times v[i]]-t\times w[i]$$ 关于 $$t$$ 单调下降，更新 $$f[b+x\times v[i]]$$ 的值。

由于每个剩余类最多只有 $$\frac{m}{v[i]}$$ 个元素，那么对于一个 $$i$$ ，转移的总时间为 $$O(\frac{m}{v[i]} \times v[i]) = O(m)$$ ，所以算法的时间复杂度为 $$O(n\times m)$$ ，这样我们就又优化掉了多重背包中的物品个数这一维。

```cpp
int maxValue(std::vector<int>& values, std::vector<int>& weight, 
             std::vector<int>& count, int n, int m){
    int dp[m + 1];
    int q[m + 1];
    int num[m + 1];
    memset(dp, 0, sizeof(dp));
    int l, r;
    for(int i = 1; i <= n; ++i){
        int c = min(count[i], m / weight[i]);
        for(int b = 0; b < weight[i]; b++){
            l = r = 1;
            for(int t = 0; t <= (m - b) / weight[i]; t++){
                int tmp = dp[t * weight[i] + b] - t * values[i];
                while(l < r && q[r - 1] <= tmp)
                    r--;
                q[r] = tmp;
                num[r++] = t;
                // 滑动区间长度不大于c，因为dp[t * weight[i] + b] - t * values[i]既然存在，
                // 那么再加c区间的t * values[i]的值肯定能取到
                while(l < r && t - num[l] > c)
                    l++;
                // 因为dp中的是t * weight[i] + b,所以是q[l] + t * values[i]
                dp[t * weight[i] + b] = max(dp[t * weight[i] + b], q[l] + t * values[i]);
            }
        }
    }
    return dp[m];
}
```

### \*\*\*\*[**Ones and Zeroes**](https://leetcode-cn.com/problems/ones-and-zeroes/) 

