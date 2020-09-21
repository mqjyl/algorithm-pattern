# 石子堆问题

## ✏ 1、任意合并【[链接](https://www.acwing.com/problem/content/150/)】

有N堆石子，现要将石子有序的合并成一堆，规定如下：每次只能移动任意的2堆石子合并，合并花费为新合成的一堆石子的数量。求将这N堆石子合并成1堆的最小花费和最大花费。

特点：合并的是任意两堆，直接贪心即可，每次选择最小的两堆合并。本问题实际上就是哈夫曼的变形。

```cpp
// 最小花费
int mergeStones(vector<int> &nums){
    if(nums.empty())
        return 0;
    sort(nums.begin(), nums.end());
    int idx = 0;
    int res = 0;
    while(idx < nums.size() - 1){
        nums[idx] += nums[idx + 1];
        res += nums[idx];
        nums[idx + 1] = 0;
        int tmp = nums[idx];
        int i = idx + 1;
        for(; i < nums.size() && nums[i] < tmp; ++i){
            nums[i - 1] = nums[i];
        }
        nums[i - 1] = tmp;
        while(nums[idx] == 0)
            idx++;
    }
    return res;
}
```

### [石子合并](https://www.nowcoder.com/questionTerminal/3eef8d66b0fa4f71a8498974547fe670?orderByHotValue=1&page=1&onlyReference=false)

```cpp
// 最大花费
int getMax(vector<int> &nums){
    sort(nums.begin(), nums.end());
    int res = 0;
    for(int i = nums.size() - 1; i > 0; --i){
        res += nums[i - 1] * nums[i];
        nums[i - 1] += nums[i];
    }
    return res;
}
```

## ✏ 2、相邻合并【[链接](https://www.luogu.com.cn/problem/P5569)】

在一个操场上摆放着一排 N 堆石子。现要将石子有次序地合并成一堆。规定每次只能选相邻的 2 堆石子合并成新的一堆，并将新的一堆石子数记为该次合并的得分。试设计一个算法，计算出将 N 堆石子合并成一堆的最小得分。

> 我们熟悉矩阵连乘，知道矩阵连乘也是每次合并相邻的两个矩阵，那么石子合并可以用矩阵连乘的方式来解决。设 $$dp[i][j]$$ 表示第 $$i$$ 到第 $$j$$ 堆石子合并的最优值， $$sum[i]$$ 表示前 $$i$$ 堆石子的总数量。那么就有状态转移公式： $$dp[i][j]=min(dp[i][k]+dp[k+1][j]+sum[j]-sum[i-1])(i\le k\le j)$$ ，且 $$i=j$$ 时 $$dp[i][j]=0$$ 。
>
> 动态规划：阶段、状态和决策，三者应该从外到里循环。

```cpp

```

 **四边形不等式优化：**平行四边形优化是一种可以将三维DP复杂度降到 $$n^2$$ 的方法，但是并不是所有的DP都适用，需要满足一定条件，如下：

* 四边形不等式：当决策代价函数 $$w[i][j]$$ 满足 $$w[i][j]+w[i'][j']\le w[i'][j]+w[i][j'](i\le i'\le j\le j')$$ 时，称 $$w$$ 满足四边形不等式；
* 区间包含的单调性：当函数 $$w[i][j]$$ 满足 $$w[i'][j]\le w[i][j'](i\le i'\le j\le j')$$ 时，称 $$w$$ 关于区间包含关系单调。

如果满足以上两点，可利用四边形不等式推出最优决策 $$s$$ 的单调函数性，从而减少每个状态的状态数，将算法的时间复杂度降低一维。具体的实施是通过记录子区间的最优决策来减少当前的决策量。令：

$$
s[i][j]=min\{k | m[i][j] = m[i][k-1] + m[k][j] + w[i][j]\}
$$

即 $$s[i] [j]$$ 就记录了合并第 $$i$$ 到第 $$j$$ 堆石子时的最优合并，记录是为了限制后面的循环范围，所以递推公式为：

$$
dp[i][j]=min(dp[i][k]+dp[k+1][j])+sum[j]-sum[i-1]\\ (s[i][j-1]\le k\le s[i+1][j])
$$

证明过程：

设 $$m[i,j]$$ 表示动态规划的状态量。 $$m[i,j]$$ 有类似如下的状态转移方程： $$m[i,j]=opt\{m[i,k]+m[k,j]\}(i\le k\le j)$$ 如果对于任意的 $$a\le b\le c\le d$$ ，有 $$m[a,c]+m[b,d]\le m[a,d]+m[b,c]$$ ，那么 $$m[i,j]$$ 满足四边形不等式。

设 $$s[i,j]$$ 为 $$m[i,j]$$ 的决策量，即 $$m[i,j]=m[i,s[i,j]]+m[s[i,j]+j]$$ ，我们可以证明， $$s[i,j-1]\le s[i,j]\le s[i+1,j]$$，那么改变状态转移方程为： $$m[i,j]=opt\{m[i,k]+m[k,j]\}(s[i,j-1]≤k≤s[i+1,j])$$ ；

**复杂度分析：**不难看出，复杂度决定于 $$s$$ 的值，以求 $$m[i,i+L]$$ 为例， $$(s[2,L+1]-s[1,L])+(s[3,L+2]-s[2,L+1])…+(s[n-L+1,n]-s[n-L,n-1])=s[n-L+1,n]-s[1,L]\le n$$ ，所以总复杂度是 $$O(n^2)$$ 。

证明过程见下：

设 $$m_k[i,j]=m[i,k]+m[k,j],s[i,j]=d$$ ，对于任意 $$k<d$$ ，有 $$m_k[i,j]\ge m_d[i,j]$$ （这里以 $$m[i,j]=min\{m[i,k]+m[k,j]\}$$ 为例，`max`的类似），接下来只要证明 $$m_k[i+1,j]\ge m_d[i+1,j]$$ ，那么只有当 $$s[i+1,j]\ge s[i,j]$$ 时才有可能有 $$m_{s[i+1,j]}[i+1,j]\le m_d[i+1,j]$$ ：

$$
\begin{split}
& (m_k[i+1,j]-m_d[i+1,j]) - (m_k[i,j]-m_d[i,j]) \\
&=(m_k[i+1,j]+m_d[i,j]) - (m_d[i+1,j]+m_k[i,j])\\
&=(m[i+1,k]+m[k,j]+m[i,d]+m[d,j]) - (m[i+1,d]+m[d,j]+m[i,k]+m[k,j])\\
&=(m[i+1,k]+m[i,d]) - (m[i+1,d]+m[i,k])
\end{split}
$$

因为 $$m$$ 满足四边形不等式，所以对于 $$i<i+1\le k<d$$ 有 $$m[i+1,k]+m[i,d]\ge m[i+1,d]+m[i,k]$$ ，所以 $$(mk[i+1,j]-md[i+1,j])\ge (mk[i,j]-md[i,j])\ge 0$$ ，所以 $$s[i,j]\le s[i+1,j]$$ ，同理可证 $$s[i,j-1]\le s[i,j]$$ 。证毕。

### \*\*\*\*[**Minimum Cost to Merge Stones**](https://leetcode-cn.com/problems/minimum-cost-to-merge-stones/)\*\*\*\*

## ✏ 3、环形合并【[链接](https://www.luogu.com.cn/problem/P1880)】

在一个圆形操场的四周摆放 N 堆石子,现要将石子有次序地合并成一堆.规定每次只能选相邻的2堆合并成新的一堆，并将新的一堆的石子数，记为该次合并的得分。试设计出一个算法,计算出将 N 堆石子合并成 1 堆的最小得分和最大得分。

## ✏ 4、分石子【[链接](https://www.nowcoder.com/questionTerminal/1ea5b4eaeff841a4918931791b000756)】

有N堆石子，第 $$i$$ 堆一共有 $$a_i$$ 个石子。可以对任意一堆石子数量大于1的石子堆进行分裂操作，分裂成两堆新的石子数量都大于等于1的石子堆。现在需要通过分裂得到m堆石子，求这m堆石子的最小值最大可以是多少？

