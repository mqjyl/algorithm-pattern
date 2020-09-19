# 背包问题

## ✏ 1、0-1背包【[链接](https://www.nowcoder.com/questionTerminal/708f0442863a46279cce582c4f508658?orderByHotValue=1&page=1&onlyReference=false)】

有`n`件物品和容量为`m`的背包，给出每件物品的重量 $$w_i$$ 以及价值 $$v_i$$ ，求解让装入背包的物品重量不超过背包容量，且价值最大 。**特点：**每个物品只有一件供你选择放还是不放。

> 二维解法：设 $$f[i][j]$$ 表示前 $$i$$ 件物品 总重量不超过 $$j$$ 的最大价值，可得出状态转移方程：
>
> $$f[i][j]=max\{f[i-1][j-a[i]]+b[i],\ f[i-1][j]\}$$ 
>
> 一维解法：设 $$f[j]$$ 表示重量不超过 $$j$$ 公斤的最大价值，可得出状态转移方程：
>
> $$f[j]=max\{f[j],\ f[j−a[i]]+b[i]\}$$

![](../.gitbook/assets/image%20%2860%29.png)

{% tabs %}
{% tab title="二维解法" %}
```cpp
int maxValue(std::vector<int>& values, std::vector<int>& weight, int m){
    int n = values.size();
    int dp[n + 1][m + 1];
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
```

```
{% endtab %}
{% endtabs %}

## ✏ 2、完全背包【链接】

![](../.gitbook/assets/image%20%2861%29.png)

## ✏ 3、多重背包（京东笔试）



