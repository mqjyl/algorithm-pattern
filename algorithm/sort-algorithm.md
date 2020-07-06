---
description: 总结常用的十大内部排序算法，并提供代码实现。
---

# 排序算法

## 1、分类

排序可分为内部排序和外部排序，常用的十种内部排序算法又可以分为两大类：

* **比较类排序**：通过比较来决定元素间的相对次序，由于其时间复杂度不能突破O\(nlogn\)，因此也称为非线性时间比较类排序。
* **非比较类排序**：不通过比较来决定元素间的相对次序，它可以突破基于比较排序的时间下界，以线性时间运行，因此也称为线性时间非比较类排序。

![&#x6392;&#x5E8F;&#x7B97;&#x6CD5;&#x5206;&#x7C7B;&#x56FE;](../.gitbook/assets/sort_algorithm.png)

## 2、算法比较

* **稳定**：如果a原本在b前面，而a=b，排序之后a仍然在b的前面。
* **不稳定**：如果a原本在b的前面，而a=b，排序之后 a 可能会出现在 b 的后面。
* **时间复杂度**：对排序数据的总的操作次数。反映当n变化时，操作次数呈现什么规律。
* **空间复杂度：**是指算法在计算机内执行时所需存储空间的度量，它也是数据规模n的函数。

![&#x5341;&#x5927;&#x5185;&#x90E8;&#x6392;&#x5E8F;&#x7B97;&#x6CD5;&#x6BD4;&#x8F83;](../.gitbook/assets/sort_algorithm.jpg)

## 3、算法实现（升序）

### **冒泡排序**

> 比较相邻的元素，如果第一个比第二个大，就交换它们两个；对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样走一轮至少保证在最后的元素应该会是最大的数；针对所有的元素重复以上的步骤，除了最后一个；重复上述过程，直到排序完成。

```cpp
vector<int> bubbleSort(vector<int>& nums) {
    int len = nums.size();
    while(len > 1){
        for(int i = 0;i < len - 1;i ++){
            if(nums[i] > nums[i+1]){
                int tmp = nums[i];
                nums[i] = nums[i + 1];
                nums[i + 1] = tmp;
            }
        }
        len--;
    }
    return nums;
}
```

### [快速排序](https://leetcode-cn.com/problems/sort-an-array/)

> 快速排序的基本思想：通过一趟排序将待排记录分隔成独立的两部分，其中一部分记录的关键字均比另一部分的关键字小，则可分别对这两部分记录继续进行排序，以达到整个序列有序。

> 快速排序使用分治法来把一个串（list）分为两个子串（sub-lists）。具体算法描述如下：

> * 从数列中挑出一个元素，称为 “基准”（pivot）；
> * 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；
> * 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。

```cpp
int partition(std::vector<int>& nums, int start, int stop){
    int idx = start + 1;
    for(int i = idx;i <= stop;i ++){
        if(nums[i] < nums[start]){
            int tmp = nums[i];
            nums[i] = nums[idx];
            nums[idx] = tmp;
            idx++;
        }
    }
    int tmp = nums[start];
    nums[start] = nums[idx - 1];
    nums[idx - 1] = tmp;
    return idx - 1;
}
void quickSort(std::vector<int>& nums, int start, int stop){
    if(start >= stop)
        return;
    int pivot = partition(nums, start, stop);
    quickSort(nums, start, pivot - 1);
    quickSort(nums, pivot + 1, stop);
}
vector<int> sortArray(vector<int>& nums) {
    quickSort(nums, 0, nums.size() - 1);
    return nums;
}
```

### 简单插入排序

> 插入排序的思想是假定前n个元素已经排好序，对于第n+1个元素，从第n个元素往前寻找，找到合适的地方插入。

```cpp
vector<int> insertionSort(vector<int>& nums){
    int len = nums.size();
    for(int i = 1;i < len; i++){
        int tmp = nums[i];
        int j;
        for(j = i - 1;j >= 0 && nums[j] > tmp;j--){
            nums[j + 1] = nums[j];
        }
        nums[j + 1] = tmp;
    }
    return nums;
}
```

### 希尔排序

> 1959年由Shell发明，第一个突破 $$O(n^2)$$ 的排序算法，是简单插入排序的改进版。它与插入排序的不同之处在于，它会优先比较距离较远的元素。希尔排序又叫**缩小增量排序**。

> 先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，具体算法描述：

> * 选择一个增量序列 $$t_1，t_2，\ldots，t_k，\text{其中}t_i > t_j，t_k=1$$；
> * 按增量序列个数 k，对序列进行 k 趟排序；
> * 每趟排序，根据对应的增量 $$t_i$$ ，将待排序列分割成若干长度为 m 的子序列，分别对各子表进行直接插入排序。仅增量因子为 1 时，整个序列作为一个表来处理，表长度即为整个序列的长度。

