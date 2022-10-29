排序是经常用到的算法，是面试、考核中的常客。基本的排序方法就不多介绍了，这里介绍一些算法的应用，以及堆排序。

## 1.数组中的第k个最大元素

link:[数组中的第K个最大元素 - 数组中的第K个最大元素 - 力扣（LeetCode）](https://leetcode.cn/problems/kth-largest-element-in-an-array/solution/shu-zu-zhong-de-di-kge-zui-da-yuan-su-by-leetcode-/)

### Description

给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。

请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

你必须设计并实现时间复杂度为 O(n) 的算法解决此问题。

示例 1:

输入: [3,2,1,5,6,4], k = 2
输出: 5
示例 2:

输入: [3,2,3,1,2,4,5,5,6], k = 4
输出: 4

### Solution

最简单的方法就是快速排序了，快速排序或者堆排序需要O(nlogn)的时间。这里介绍基于"快速排序"的快速选择算法，这个算法可以在O(n)的时间内找到结果。

首先来回顾一下快速排序吧，快速排序是很经典的分治算法，它的思想是选出一个元素，使其"归位"，即小于它的元素在它左边，大于它的元素在它右边。然后对左边和右边分别进行快速排序。

这个过程如何简化呢？在这里我们只需要第k大的元素，也就是这个元素"归位"后的位置是n-k+1。那么，我们在一次快速排序中，假设归位的元素位置是i，那么如果i>n-k+1，那么我们只需要对i的左边进行快速排序，右边不需要。这样就可以把原来递归两个区间变成只分治一个区间，从而加速算法。

我们知道，快速排序的最坏时间复杂度是$O(n^2)$，引入随机化加速过程后，时间复杂度的期望为$O(nlogn)$。同理，引入随机化后，该"快速选择算法"的时间复杂度是$O(n)$。证明过程见	《算法导论》9.2：期望为线性的选择算法。

该算法学习自：[数组中的第K个最大元素 - 数组中的第K个最大元素 - 力扣（LeetCode）](https://leetcode.cn/problems/kth-largest-element-in-an-array/solution/shu-zu-zhong-de-di-kge-zui-da-yuan-su-by-leetcode-/)

### Code

```c++
class Solution1 {
public:
    //quickSelect
    int quickSelect(vector<int>& nums, int l, int r, int index)
    {
        int q = RandPartition(nums, l, r);
        if (q == index) return nums[index];
        else return q<index?quickSelect(nums,q+1,r,index): quickSelect(nums, l, q - 1, index);
    }
    //find a random number's index in[l,r]
    int RandPartition(vector<int>& nums, int l, int r)
    {
        int i = rand() % (r - l + 1) + l;
        //let a random index to be partitioned
        swap(nums[i], nums[r]);
        return partition(nums, l, r);
    }
    //find nums[r]'s index in [l,r]
    int partition(vector<int>& nums, int l, int r)
    {
        int num = nums[r],i=l-1;
        for (int j = l; j < r; j++)
        {
            if (nums[j] <= num) swap(nums[++i], nums[j]);
        }
        swap(nums[i+1],nums[r]);
        //return index of primary nums[r]
        return i + 1;
    }

    int findKthLargest(vector<int>& nums, int k) {
        srand(time(0));
    }
};
```

## 2.颜色分类

link:[75. 颜色分类 - 力扣（LeetCode）](https://leetcode.cn/problems/sort-colors/)

### Description

给定一个包含红色、白色和蓝色、共 n 个元素的数组 nums ，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

必须在不使用库的sort函数的情况下解决这个问题。

### Solution

最简单的方法就是统计三种元素出现的次数，然后重新构造一个数组。此外，本题只有三种元素，很适合使用双指针来解决问题。

我们可以用一个指针p0来指代0，另一个指针p2指代2.p0从位置0开始从左向右移动，p2从位置length-1开始从右向左移动。

我们从左向右遍历数组，然后如果发现一个0，就与p0处的元素交换，然后p0向右移动一个位置；如果发现一个2，就与p2处的元素交换，然后p2向左移动一个位置。这样做可以保证结果正确吗？

![image-20220814174252770](https://cdn.jsdelivr.net/gh/wuyun666/chartBed@main/img/image-20220814174252770.png)

如图，是数组[0,1,2,0,1,2,0,1,2]经过若干次上述操作后得到的数组，p0、p2都处于特定的位置，正在遍历的元素是2.当被遍历的元素与p2指针交换之后，且p2左移后，得到如下结果：

![image-20220814174444741](https://cdn.jsdelivr.net/gh/wuyun666/chartBed@main/img/image-20220814174444741.png)

可以发现有一个元素0被跳过了，如何避免呢？

只要在被遍历的元素与p0/p2交换时，不断检查被遍历的元素位置上的元素，直到其不为0也不为2，才能让被遍历的元素的位置向右移一个位置。因为遍历到位置i时，位置p0~i上的元素必定为1，但是位置i~p2上的元素可能为0、1、2，所以对于位置i，可能有多个连续的2与其交换，但是至多只有1个0与之交换。所以指针i一定在指针p0的前面或与之重合，所以只需要考虑p2指针越界的情况即可。

### Code

```c++
class Solution2 {
public:
    void sortColors(vector<int>& nums) {
        int length = nums.size();
        int p0 = 0, p2 = length - 1, i = 0;
        int temp = 0;
        for (i = 0; i <=p2; i++)
        {
            while (nums[i] == 2 && i <= p2)
            {
                swap(nums[i], nums[p2]);
                p2--;
            }
            if (nums[i] == 0)
            {
                swap(nums[i], nums[p0]);
                p0++;
            }
        }
    }
};
```



