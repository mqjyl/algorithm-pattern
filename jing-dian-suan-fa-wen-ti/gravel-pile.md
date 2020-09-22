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

## ✏ 2、相邻合并【[链接](https://www.luogu.com.cn/problem/P5569)】**（超时，超空间）**

在一个操场上摆放着一排 N 堆石子。现要将石子有次序地合并成一堆。规定每次只能选相邻的 2 堆石子合并成新的一堆，并将新的一堆石子数记为该次合并的得分。试设计一个算法，计算出将 N 堆石子合并成一堆的最小得分。

> 我们熟悉矩阵连乘，知道矩阵连乘也是每次合并相邻的两个矩阵，那么石子合并可以用矩阵连乘的方式来解决。设 $$dp[i][j]$$ 表示第 $$i$$ 到第 $$j$$ 堆石子合并的最优值， $$sum[i]$$ 表示前 $$i$$ 堆石子的总数量。那么就有状态转移公式： $$dp[i][j]=min(dp[i][j],dp[i][k]+dp[k+1][j]+sum[j]-sum[i-1])(i\le k< j)$$ ，且 $$i=j$$ 时 $$dp[i][j]=0$$ 。
>
> 动态规划：**阶段、状态和决策，三者应该从外到里循环。**
>
> * 阶段v：石子的每一次合并过程，先两两合并，再三三合并，…最后N堆合并 
> * 状态：每一阶段中各个不同合并方法的石子合并总得分。 
> * 决策：把当前阶段的合并方法细分成前一阶段已计算出的方法，选择其中的最优方案

> 具体来说我们应该定义一个数组 $$dp[i][j]$$ 用来表示合并方法， ****$$dp[i][j]$$ 表示从第 $$i$$ 堆开始数 $$j$$ 堆进行合并的最优得分。

```cpp
int mergeStones(std::vector<int> &nums){
    int len = nums.size();
    int sum[len + 1];
    sum[0] = 0;
    int dp[len + 1][len + 1];
    for(int i = 1; i <= len; ++i){
        sum[i] = sum[i - 1] + nums[i - 1];
        dp[i][i] = 0;
    }
    for(int v = 1; v < len; ++v){
        for(int i = 1; i <= len - v; ++i){
            int j = i + v;
            dp[i][j] = INT_MAX;
            for(int k = i; k < j; ++k){
                dp[i][j] = min(dp[i][j], dp[i][k] + dp[k + 1][j]);
            }
            dp[i][j] += sum[j] - sum[i - 1];
        }
    }
    return dp[1][len];
}
```

###  **四边形不等式优化（超空间）**

平行四边形优化是一种可以将三维DP复杂度降到 $$n^2$$ 的方法，但是并不是所有的DP都适用，需要满足一定条件，如下：

* 四边形不等式：当决策代价函数 $$w[i][j]$$ 满足 $$w[i][j]+w[i'][j']\le w[i'][j]+w[i][j'](i\le i'\le j\le j')$$ 时，称 $$w$$ 满足四边形不等式；
* 区间包含的单调性：当函数 $$w[i][j]$$ 满足 $$w[i'][j]\le w[i][j'](i\le i'\le j\le j')$$ 时，称 $$w$$ 关于区间包含关系单调。

如果满足以上两点，可利用四边形不等式推出最优决策 $$s$$ 的单调函数性，从而减少每个状态的状态数，将算法的时间复杂度降低一维。具体的实施是通过记录子区间的最优决策来减少当前的决策量。令：

$$
s[i][j]=min\{k | dp[i][j] = dp[i][k-1] + dp[k][j]\}
$$

即 $$s[i] [j]$$ 就记录了合并第 $$i$$ 到第 $$j$$ 堆石子时的最优合并，记录是为了限制后面的循环范围，所以递推公式为：

$$
dp[i][j]=min(dp[i][k]+dp[k+1][j])+sum[j]-sum[i-1]\\ (s[i][j-1]\le k\le s[i+1][j])
$$

**证明过程：**

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

因为 $$m$$ 满足四边形不等式，所以对于 $$i<i+1\le k<d$$ 有 $$m[i+1,k]+m[i,d]\ge m[i+1,d]+m[i,k]$$ ，所以 $$(mk[i+1,j]-md[i+1,j])\ge (mk[i,j]-md[i,j])\ge 0$$ ，所以 $$s[i,j]\le s[i+1,j]$$ ，同理可证 $$s[i,j-1]\le s[i,j]$$ ，证毕。

```cpp
int mergeStones(std::vector<int> &nums)
{
    int len = nums.size();
    int sum[len + 1];
    sum[0] = 0;
    int dp[len + 1][len + 1];
    int s[len + 1][len + 1];
    for(int i = 1; i <= len; ++i){
        sum[i] = sum[i - 1] + nums[i - 1];
        dp[i][i] = 0;
        s[i][i] = i;
    }
    for(int v = 1; v < len; ++v){
        for(int i = 1; i <= len - v; ++i){
            int j = i + v;
            dp[i][j] = INT_MAX;
            for(int k = s[i][j - 1]; k <= s[i + 1][j]; ++k){
                if(dp[i][j] > dp[i][k] + dp[k + 1][j]) {
                    dp[i][j] = dp[i][k] + dp[k + 1][j];
                    s[i][j] = k;
                }
            }
            dp[i][j] += sum[j] - sum[i - 1];
        }
    }
    return dp[1][len];
}
```

###  **`GarsiaWachs`算法（AC）**

对于石子合并问题，有一个最好的算法，那就是`GarsiaWachs`算法。时间复杂度为 ****$$O(n^2)$$ 。

算法步骤：设序列是`stone[]`，从左往右，找一个满足 $$stone[k-1] \le stone[k+1]$$ 的 $$k$$ ，找到后合并 $$stone[k]$$ 和 $$stone[k-1]$$ ，再从当前位置开始向左找最大的 $$j$$ ，使其满足 $$stone[j] > stone[k]+stone[k-1]$$ ，插到 $$j$$ 的后面就行。一直重复，直到只剩下一堆石子。在这个过程中，可以假设 $$stone[-1]$$ 和 $$stone[n]$$ 是正无穷的。

基本思想是通过树的最优性得到一个节点间深度的约束，之后证明操作一次之后的解可以和原来的解一一对应，并保证节点移动之后他所在的深度不会改变。具体实现这个算法需要一点技巧，精髓在于不停快速寻找最小的 $$k$$ ，即维护一个“2-递减序列”，朴素的实现的时间复杂度是 $$O(n^2)$$ ，但可以用一个平衡树来优化，使得最终复杂度为 $$O(nlogn)$$ 。

```cpp
int mergeStones(std::vector<int> &nums){
    int ans = 0;
    int len = nums.size();
    int cnt = len - 3;
    while(cnt--){ // len - 2个石子堆总共合并 len - 3 次
        int tmp = 0;
        int i = 2;
        for(; i < len - 1; ++i){
            if(nums[i - 1] <= nums[i + 1]){
                tmp = nums[i - 1] + nums[i];
                ans += tmp;
                break;
            }
        }
        int j = i - 1;
        // 将 tmp 插入到合适的位置
        for(; nums[j - 1 ] < tmp; j--)
            nums[j] = nums[j - 1];
        nums[j] = tmp;
        // 删除第 i 个元素
        nums.erase(nums.begin() + i);  // 这样写就不超时，下面的写法就超时
//        for(j = i; j < len - 1 ; j++)
//            nums[j] = nums[j + 1];
        len--;
    }
    return ans;
}

int main(){
    int n;
    cin >> n;
    vector<int> nums(n + 2);
    nums[0] = INT_MAX;  // 两头加两个正无穷，有助于处理边界条件
    int i = 1;
    while(i <= n){
        cin >> nums[i];
        ++i;
    }
    nums[n + 1] = INT_MAX;
    cout << mergeStones(nums);
    return 0;
}
```

### \*\*\*\*[**Minimum Cost to Merge Stones**](https://leetcode-cn.com/problems/minimum-cost-to-merge-stones/)\*\*\*\*

有 N 堆石头排成一排，第 i 堆中有 `stones[i]` 块石头。每次移动（move）需要将连续的 K 堆石头合并为一堆，而这个移动的成本为这 K 堆石头的总数。找出把所有石头合并成一堆的最低成本。如果不可能，返回 -1 。

## ✏ 3、环形合并【[链接](https://www.luogu.com.cn/problem/P1880)】

在一个圆形操场的四周摆放 N 堆石子,现要将石子有次序地合并成一堆.规定每次只能选相邻的2堆合并成新的一堆，并将新的一堆的石子数，记为该次合并的得分。试设计出一个算法,计算出将 N 堆石子合并成 1 堆的最小得分和最大得分。

> 设 $$dp[i][j]$$ 表示第 $$i$$ 堆开始合并 $$j$$ 堆石子的最优值， $$sum[i,j]$$ 表示第 $$i$$ 堆到第 $$j$$ 堆石子的总数量。那么就有状态转移公式： $$dp[i][j]=min(dp[i][j],dp[i][k]+dp[(i+k-1)\%n+1][j-k]+sum[i,j])$$ ，且 $$i=j$$ 时 $$dp[i][j]=0$$ 。
>
> * $$k$$ 是 $$i$$ 和 $$j$$ 之间的一个数
> * 因为是环形，所以要将一维数组收尾相连，所以计算第 $$i+k$$ 堆应该表示成： $$(i+k-1)\%n+1$$ ，注意这里不能想当然的以为 $$-1\%n$$ 可以和外面的1约掉就变成 $$(k+1)\%n$$ ，这里是因为数组的下标是从1开始的，前者可以保证不取到0。
>
> 然后求 $$dp[i][n]$$ 的最小解， $$1\le i\le n$$ 。

```cpp
int sum(std::vector<int> &nums, int i, int j){
    int ans = 0;
    for(; j > 0; j--, i++){
        if(i > nums.size() - 1)
            i %= (nums.size() - 1);
        ans += nums[i];
    }
    return ans;
}
pair<int, int> DynamicProgramming::mergeStones_ii(std::vector<int> &nums){
    int len = nums.size();
    int dp_min[len][len];
    int dp_max[len][len];
    for(int i = 1; i < len; i++){
        dp_min[i][1] = 0; // 没有合并则花费为0
        dp_max[i][1] = 0;
    }
    for(int j = 2; j < len; ++j){
        for(int i = 1; i < len; ++i){
            dp_min[i][j] = INT_MAX;
            dp_max[i][j] = INT_MIN;
            for(int k = 1; k < j; k++){
                dp_min[i][j] = min(dp_min[i][j], dp_min[i][k] + dp_min[(i + k - 1) 
                               % (len - 1) + 1][j - k] + sum(nums, i, j));
                dp_max[i][j] = max(dp_max[i][j], dp_max[i][k] + dp_max[(i + k - 1) 
                               % (len - 1) + 1][j - k] + sum(nums, i, j));
            }
        }
    }
    int mini = INT_MAX;
    int maxi = INT_MIN;
    for(int i = 1; i < len; i++){//从第几堆石子开始结果最小
        mini = min(mini, dp_min[i][len - 1]);
        maxi = max(maxi, dp_max[i][len - 1]);
    }
    return make_pair(mini, maxi);
}

int main(){
    int n;
    cin >> n;
    vector<int> nums(n + 1);
    nums[0] = 0;
    int i = 1;
    while(i <= n){
        cin >> nums[i];
        i++;
    }
    pair<int, int> res = mergeStones(nums);
    cout << res.first << endl;
    cout << res.second << endl;
    return 0;
}
```

