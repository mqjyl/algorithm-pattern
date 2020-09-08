# 数 & 数组 & 矩阵

## ✏ 数



## ✏ 数组

### 🖋 1、[**数组中出现次数超过一半的数字**](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/)\*\*\*\*

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。

> 方法一：哈希表
>
> 方法二：摩尔投票

{% tabs %}
{% tab title="哈希表" %}
```cpp
int majorityElement(vector<int>& nums) {
    int len = nums.size();
    unordered_map<int, int> imap;
    for(int num : nums){
        imap[num]++;
        if(imap[num] > len / 2)
            return num;
    }
    return 0;
}
```
{% endtab %}

{% tab title="摩尔投票" %}
```cpp
int majorityElement(std::vector<int>& nums){
    int votes = 0;
    int num = nums[0];
    for(int i = 1; i < nums.size(); ++i){
        votes = nums[i] != num ? votes - 1 : votes + 1;
        if(votes < 0){
            num = nums[i];
            votes = 0;
        }
    }
    return num;
}
```
{% endtab %}
{% endtabs %}

### \*\*\*\*🖋 **2、**[**部分排序**](https://leetcode-cn.com/problems/sub-sort-lcci/)\*\*\*\*

给定一个整数数组，编写一个函数，找出索引m和n，只要将索引区间\[m,n\]的元素排好序，整个数组就是有序的。注意：n-m尽量最小，也就是说，找出符合条件的最短序列。函数返回值为\[m,n\]，若不存在这样的m和n（例如整个数组是有序的），请返回\[-1,-1\]。

> 方法一：将原数组复制一份后排序，和原数组两头比较。时间复杂度 $$O(nlogn)$$ 空间复杂度 $$O(n)$$ 
>
> 方法二：一趟遍历+双指针。时间复杂度 $$O(n)$$ 空间复杂度 $$O(1)$$ 
>
> 1. 从前向后扫描数组，判断当前`array[i]`是否比`max`小，是则将`right`置为当前`array`下标`i`，否则更新`max`；
> 2. 从后向前扫描数组，判断当前`array[len - 1 - i]`是否比`min`大，是则将`left`置位当前下标`len - 1 - i`，否则更新`min`；
> 3. 返回`{left， right}`。

{% tabs %}
{% tab title="排序" %}
```cpp
vector<int> subSort(vector<int>& array) {
    vector<int> tmp = array;
    sort(tmp.begin(), tmp.end());
    int left = -1, right = -1;
    for(int i = 0; i < array.size(); i++){
        if(tmp[i] != array[i]){
            if(left == -1)
                left = i;
            right = i;
        }
    }
    return vector<int>{left, right};
}
```
{% endtab %}

{% tab title="双指针" %}
```cpp
vector<int> subSort(vector<int>& array) {
    int left = -1, right = -1;
    int max_val = INT_MIN;
    int min_val = INT_MAX;
    int len = array.size();
    for(int i = 0; i < len; i++){
        if(array[i] < max_val){
            right = i;
        }else{
            max_val = max(max_val, array[i]);
        }
        if(array[len - i - 1] > min_val){
            left = len - i - 1;
        }else{
            min_val = min(min_val, array[len - i - 1]);
        }
    }
    return vector<int>{left, right};
}
```
{% endtab %}
{% endtabs %}

### \*\*\*\*🖋 **3、**[数组中未出现的最小正整数](https://www.nowcoder.com/questionTerminal/030cabe03d94484c819e87c2e38c41bd?answerType=1&amp;f=discussion)

给定一个无序数组arr，找到数组中未出现的最小正整数 

例如`arr = [-1, 2, 3, 4]`，返回1；`arr = [1, 2, 3, 4]`，返回5。

\[要求\] 时间复杂度为 $$O(n)$$ ，空间复杂度为 $$O(1)$$ 。

>

{% tabs %}
{% tab title="一维数组" %}
```cpp
int findMin(std::vector<int> &arr){
    int left = 0, right = arr.size();
    while(left < right){
        int tmp = arr[left];
        if(tmp == left + 1){
            left++;
        }else if(tmp < left + 1 || tmp > right || arr[tmp - 1] == tmp){
            right--;
            arr[left] = arr[right];
        }else{
            swap(arr[left], arr[tmp - 1]);
        }
    }
    return left + 1;
}
```
{% endtab %}

{% tab title="二维数组" %}
```cpp
int findMin(std::vector<std::vector<int> > &arr){
    int rlen = arr.size();
    int clen = arr[0].size();
    int left = 0, right = rlen * clen;
    while(left < right){
        int tmp = arr[left / clen][left % clen];
        if(tmp == left + 1){
            left++;
        }else if(tmp < left + 1 || tmp > right || 
                 arr[(tmp - 1) / clen][(tmp - 1) % clen] == tmp){
            right--;
            arr[left / clen][left % clen] = arr[right / clen][right % clen];
        }else{
            swap(arr[left / clen][left % clen], 
                 arr[(tmp - 1) / clen][(tmp - 1) % clen]);
        }
    }
    return left + 1;
}
```
{% endtab %}
{% endtabs %}

## ✏ 矩阵

### 🖋 1、[螺旋矩阵](https://leetcode-cn.com/problems/spiral-matrix/)

{% tabs %}
{% tab title="循环控制" %}
```cpp
vector<int> spiralOrder(vector<vector<int>>& matrix) {
    vector<int> result;
    if(matrix.empty())
        return result;
    int row_s = 0, row_t = matrix.size() - 1;
    int col_s = 0, col_t = matrix[0].size() - 1;

    while(true){
        if(row_s <= row_t){
            for(int i = col_s; i <= col_t; ++i)
                result.push_back(matrix[row_s][i]);
            ++row_s;
            if(row_s > row_t)
                break;
        }
        if(col_s <= col_t){
            for(int i = row_s; i <= row_t; ++i)
                result.push_back(matrix[i][col_t]);
            --col_t;
            if(col_s > col_t)
                break;
        }
        if(row_s <= row_t){
            for(int i = col_t; i >= col_s; --i)
                result.push_back(matrix[row_t][i]);
            --row_t;
            if(row_s > row_t)
                break;
        }
        if(col_s <= col_t){
            for(int i = row_t; i >= row_s; --i)
                result.push_back(matrix[i][col_s]);
            ++col_s;
            if(col_s > col_t)
                break;
        }
    }
    return result;
}
```
{% endtab %}

{% tab title="静态列表" %}
```

```
{% endtab %}
{% endtabs %}

### 🖋 2、对角打印

将一个矩阵（二维数组）按对角线向右进行打印。

```cpp
vector<int> slashOrder(vector<vector<int> > &matrix) {
    int cols = matrix[0].size();
    int rows = matrix.size();
    vector<int> result;
    for (int l = 0; l < cols + rows - 1; l++) {
        int sum = l;
        for (int i = 0; i < rows; i++) {
            int j = sum - i;
            if (i >= 0 && i < rows && j >= 0 && j < cols) {
                result.push_back(matrix[i][j]);
            }
        }
    }
    return result;
}
```

