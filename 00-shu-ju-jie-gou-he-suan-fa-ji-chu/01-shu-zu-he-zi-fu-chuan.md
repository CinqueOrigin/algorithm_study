# 01-数组和字符串

## 数组练习

### 1.只出现一次的数字

link:[136. 只出现一次的数字 - 力扣（LeetCode）](https://leetcode.cn/problems/single-number/)

#### Description

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

说明：

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

示例 1:

输入: \[2,2,1] 输出: 1 示例 2:

输入: \[4,1,2,1,2] 输出: 4

#### Solution

有两个比较容易想到的实现方法，但是都不满足题意。

首先是排序+1次遍历，但是排序的时间超过了线性时间。

或者是使用一个映射数组A，A\[i]=j表示数字i出现了j次。

到现在为止复杂度存在问题，表明有我们信息利用不充分，或者是我们在遍历的过程中重复使用了信息。

本题中有一句：除了某个元素只出现一次以外，其余每个元素均出现两次。

如何能够使用这个特性呢？这让我想到级数中的交错级数，两个相等的数一正一负便可抵消。

这里我是怎么想都没想出来，直接上结论吧。

首先了解一下异或和异或的交换律。

$$
a\oplus b\oplus c=a\oplus c\oplus b
$$

如果a、b相等 ，那么a异或b的结果就是0，又因为该运算满足交换律，所以如果让所有数作异或，那么相当于把相等的数安排在一起，这样的话，结果就是那一个剩下的唯一的数。

#### code

```
class Solution1 {
public:
    int singleNumber(vector<int>& nums) {
        int answer = 0;
        for (int i = 0; i < nums.size(); i++)
        {
            answer ^= nums[i];
        }
        return answer;
    }
};
```

### 2.寻找多数元素

link:[169. 多数元素 - 力扣（LeetCode）](https://leetcode.cn/problems/majority-element/)

#### Description

给定一个大小为 n 的数组 nums ，返回其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

示例 1：

输入：nums = \[3,2,3] 输出：3 示例 2：

输入：nums = \[2,2,1,1,1,2,2] 输出：2

#### Solution

首先能想到的方法是进行一次排序，然后再遍历一次验证nums\[i]==nums\[i+n/2]即可。另外，使用跟题1提到的映射方法也可以，但是有很大的空间开销。

我们可以使用下面两条数学结论中的任意一条来减少运算次数。

$$
①若一个存在多数元素的数组去掉两个不同的元素，则之前的多数元素仍然是多数元素。 \\②如果数 a 是数组 nums 的多数元素，如果我们将 nums 分成两部分，那么 a 必定是至少一部分的多数元素。
$$

方法①：寻找多数元素是一个很经典的递归算法，它首先使用了一条定理来减少运算次数。

那么，我们只需要==不断去掉两个不同的元素==，最后剩下的元素可能就是多数元素，只要再验证这个元素出现次数是否超过一半即可。

如何不断去除两个不同元素，我们用一个count变量计数，从下标0开始（选择nums\[0]为候选元素），循环验证：如果数组的元素等于候选元素，那么count+1，否则count-1.如果循环的过程中count小于0了，那么就退出循环，将当前候选元素的下一个元素作为候选元素，重复进行验证。

这个方法只需要线性时间。

方法②：寻找多数元素也是一个很经典的分治算法，把整个数组分为相等的两份（或者数量差1），然后求分开后的两个数组的多数元素，最后再根据求出的两个多数元素求整个数组的多数元素。

两个结论都不难想到，下面提供两种方法的代码。

#### Code

**Solution1**

```
class Solution2_1 {
public:
    int findCandidate(vector<int>& nums, int index)
    {
        int totalNum = nums.size();
        if (index == nums.size()) return -1;
        int count = 1;
        int candidate = nums[index];
        for (int i = index + 1; i < totalNum; i++)
        {
            if (nums[i] == candidate) count++;
            else count--;
            if (count < 0) return findCandidate(nums, i);    //这个过程就体现了定理①，只不过是重复使用了多次
        }
        return candidate;
    }
    int majorityElement(vector<int>& nums) {
        int candidate = findCandidate(nums, 0);
        int count = 0;
        for (int i = 0; i < nums.size(); i++)
        {
            if (nums[i] == candidate)count++;
            else count--;
        }
        return candidate;
    }
};
```

**Solution2**

```
class Solution2_2 {
public:
    int getModeInArray(vector<int>& nums, int low, int high)
    {
        if (high <= low) return nums[low];
        else {
            //分治的过程
            int rightMode = getModeInArray(nums, low,(low+high)/2);
            int leftMode = getModeInArray(nums, ((low + high) / 2 + 1), high);
            //合并的过程
            int lCount=0, rCount = 0;
            //-1表示没有结果
            if (leftMode == -1 && rightMode == -1) return -1;
            for (int i = low; i <= high; i++)
            {
                if (nums[i] == leftMode) lCount++;
                else if (nums[i] == rightMode) rCount++;
            }
            if (lCount >= (high - low + 1) / 2)
                return leftMode;
            else if (rCount >= (high - low + 1) / 2)
                return rightMode;
            else return -1;

        }

    }
    int majorityElement(vector<int>& nums) {
        int result = getModeInArray(nums, 0, nums.size() - 1);
        cout << result << endl;
        return result;
    }
};
```

### 3.三数之和

link:[15. 三数之和 - 力扣（LeetCode）](https://leetcode.cn/problems/3sum/)

#### Description

给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。

注意：答案中不可以包含重复的三元组。

示例 1：

输入：nums = \[-1,0,1,2,-1,-4] 输出：\[\[-1,-1,2],\[-1,0,1]] 示例 2：

输入：nums = \[] 输出：\[] 示例 3：

输入：nums = \[0] 输出：\[]

#### Solution

最简单的方法就是三层循环嵌套了，这样的话时间复杂度是 O$(n^3)$.还是那句话，复杂度高要么做了重复的运算，要么有信息没有利用到。显然，纯三层循环的话，会增加很多没必要的运算。不要求线性时间的话，我们其实可以首先**给数组排序**，这也是最常用的处理方法。排序后的数组携带着庞大的信息，如何加以利用？我们在这里可以借鉴快速排序的**双指针**思想，快速排序是维护两个指针，最后根据他们的大小交换位置。这里我们一旦确定了第一个数a，那么只需要查找剩下两个数b和c，这里的b和c我们可以利用双指针，可以保证不会错过任何一种情况。

可以通过这个例题来了解双指针：[Loading Question... - 力扣（LeetCode）](https://leetcode.cn/problems/two-sum/)。

这样的算法，双指针最后碰头，如果两个指针移动区间长度是n，那么两个指针一共移动了n次，而在三次循环的算法中，移动了n(n-1)次，简化了时间复杂度，时间复杂度是$$O(n^2)$$。

还有一个问题，不能出现重复，这个如何做到呢？其实对数组排序之后，就简单很多了。如果位于i、j、k处的三个数凑成了0（i\<j\<k），那么在双指针（i和k）向内靠拢的时候，i指针跳过和nums\[i]相等的元素，j指针跳过和nums\[j]相等的元素即可。

#### Code

```
class Solution3 {
public:

    vector<vector<int>> threeSum(vector<int>& nums) {
        int size = nums.size();
        vector<vector<int>> result;
        //1.排序
        sort(nums.begin(),nums.end());
        //2.外层循环：确定a
        int i , j , k ;
        for (i = 0; i < size; i++)
        {
            //排除重复情况：如果与上一个数相同，那么跳过该次循环。
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            int Sum = 0 - nums[i];
            k = size - 1;
            //3.内层循环，利用双指针j和k确定b和c
            for (j = i + 1; j < nums.size(); j++)
            {
                //排除重复情况：如果与上一个数相同，那么跳过该次循环。
                if (j > i + 1 && nums[j] == nums[j - 1]) continue;

                //如果b和c的和大于-a，那么移动指针k，而且要保证移动之后k大于j
                while (k>j&&nums[j] + nums[k] > Sum)k--;

                //如果退出上面的while循环是因为k==j了，就进行下一次循环，因为对于当前的a没有解
                if (k == j) break;

                //如果退出上面的while循环是因为nums[j] + nums[k] <= Sum了，那么继续判断是否nums[j] + nums[k] == Sum
                //①如果相等，那么存在解，继续移动双指针寻找
                if (nums[j] + nums[k] == Sum) result.push_back({nums[i],nums[j],nums[k]});
                //②如果不相等，那么应该移动j指针，继续寻找下一个解，这里不做处理即可。
            }
        }
        return result;
    }
};
```

### 4.合并区间

link:[56. 合并区间 - 力扣（LeetCode）](https://leetcode.cn/problems/merge-intervals/)

#### Description

以数组 intervals 表示若干个区间的集合，其中单个区间为 intervals\[i] = \[starti, endi] 。请你合并所有重叠的区间，并返回 一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间 。

示例 1：

输入：intervals = \[\[1,3],\[2,6],\[8,10],\[15,18]] 输出：\[\[1,6],\[8,10],\[15,18]] 解释：区间 \[1,3] 和 \[2,6] 重叠, 将它们合并为 \[1,6]. 示例 2：

输入：intervals = \[\[1,4],\[4,5]] 输出：\[\[1,5]] 解释：区间 \[1,4] 和 \[4,5] 可被视为重叠区间。

#### Solution

乍一看这道题无从下手，但是我们可以尝试合并区间，发现合并区间其中需要的信息，首先考虑两个区间的情况。

如果两个区间没有交集，那么我们保留这两个区间；如果两个区间有交集，那么取两个区间的并集即可。

如果要推广到多个区间，那么我们只需要按照一定的顺序对数组进行上述两两合并的操作即可。关键问题时，如果在考虑第i个区间的合并时，同时尝试与其他区间合并，那会产生非常大的冗余。实际上，只要将这些区间按照左端点大小排序，那么合并第i个区间时只需要考虑与前面相邻的区间合并即可。为什么？假设前面k个区间和合并没有遗漏（合并后的区间互不相交），那么在合并第k+1个时，因为是按照左端点大小拍过序的，那么第k+1个区间的左端点必然大于k个区间合并后的区间集中的任一区间的左端点，且他们互不相交，所以只有合并后的区间集的最后一个区间可能与第k+1个区间相交。这样的话，第k+1个区间合并后，前k+1个区间也互不相交。根据数学归纳法，这样的做法是正确的。由于需要排序，这个方法的时间复杂度是$$O(nlogn)$$，这就是简单的动态规划思想。

#### Code

```
class Solution4 {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        if (intervals.size() == 0) return {};
        //1.sort
        sort(intervals.begin(), intervals.end());
        vector<vector<int>> mergedVector;
        //2.add the first section
        int left = intervals[0][0], right = intervals[0][1];
        mergedVector.push_back({left,right});
        //3.for each section,try to add it into mergedVector
        for (int i = 1; i < intervals.size(); i++)
        {
            left = intervals[i][0], right = intervals[i][1];
            //3.1.case 1
            if (left > mergedVector.back()[1])
                mergedVector.push_back({ left,right });
            //3.2 case2
            else {
                mergedVector.back()[1] = right> mergedVector.back()[1]?right: mergedVector.back()[1];
            }

        }
    }
};
```

### 5.搜索二维矩阵

link:[240. 搜索二维矩阵 II - 力扣（LeetCode）](https://leetcode.cn/problems/search-a-2d-matrix-ii/)

#### Description

编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target 。该矩阵具有以下特性：

每行的元素从左到右升序排列。 每列的元素从上到下升序排列。

输入：matrix = \[\[1,4,7,11,15],\[2,5,8,12,19],\[3,6,9,16,22],\[10,13,14,17,24],\[18,21,23,26,30]], target = 5 输出：true

输入：matrix = \[\[1,4,7,11,15],\[2,5,8,12,19],\[3,6,9,16,22],\[10,13,14,17,24],\[18,21,23,26,30]], target = 20 输出：false

#### Solution

比较容易想到的办法是：把每行或者每列看作一个区间，判断要找的元素是否在各个区间内，如果在某个区间内，判断这个元素是否存在于该行/该列即可.这种算法的时间复杂度并不高。如果把每行看作一个区间，列区间需要O(m)，比较需要O(m),每行的寻找需要O(n)，最坏情况下来需要O(mn)的时间。如果区间内改用二分查找，可以将查找时间减少。

还是那句话，如果时间复杂度比较大，要么是有没有利用到的信息，要么是做了不必要的计算。我们可以借鉴很经典的线性时间查找算法"寻找中项和第k小元素"其中的思想和双指针思想，来减少的比较运算：对于位于\[i,j]位置的元素，有以下的性质"==它左、上方的元素全部都比它小；它右、下方的元素全部都比他大=="。那么在比较matrix \[i] \[j]和目标元素时，如果target>matrix \[i] \[j],那么可以直接派出掉位置\[i,j]左上方的所有元素（包含该行该列）;如果target\<matrix \[i] \[j],那么可以直接派出掉位置\[i,j]右下方的所有元素（包含该行该列）;如果相等那么可以直接返回已找到。

如果我们按照上面的方法，从左上角或者右下角开始遍历，那么每次排除掉区域后，剩下的区域是不规则的形状，会分成三个区域。我们当然可以在三个区域内分别查找（相当于是分治了），这样的做法当然是没问题的。但是，如果我们从左下角或右上角开始遍历，（为了方便假设我们从右上角开始遍历）那么每次正好可以排除一行或一列；排除后，剩下的区域仍然是一个矩形，按照这种方法，最多需要排除m+n个行列，时间复杂度是O(m+n)。

举个例子，假如从右上方开始遍历：

![image-20220731165135108](https://cdn.jsdelivr.net/gh/wuyun666/chartBed@main/img/image-20220731165135108.png)

那么当前右上角元素为18\<targer=30，可以排除掉左、上方的所有元素，而这里的左、上方只有2\~18的这一行，所以比较之后剩余部分为：![image-20220731165308116](https://cdn.jsdelivr.net/gh/wuyun666/chartBed@main/img/image-20220731165308116.png)

同理，如果当前剩余部分右上角元素>target，那么可以直接排除掉这一列（右、下方）。

每次都能排除一行或一列，最多需要O(m+n)的时间。

#### Code

```
class Solution5 {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int rows = matrix.size(), cols = matrix[0].size();
        int i = 0, j = cols - 1;//右上角的元素
        while (i < rows && j >= 0)
        {
            if (target == matrix[i][j]) return true;
            else if (target > matrix[i][j]) i++;
            else j--;
        }
        return false;
    }
};
```

### 6.无重叠区间

link:[435. 无重叠区间 - 力扣（LeetCode）](https://leetcode.cn/problems/non-overlapping-intervals/)

#### Description

给定一个区间的集合 intervals ，其中 intervals\[i] = \[starti, endi] 。返回 需要移除区间的最小数量，使剩余区间互不重叠 。

示例 1:

输入: intervals = \[\[1,2],\[2,3],\[3,4],\[1,3]] 输出: 1 解释: 移除 \[1,3] 后，剩下的区间没有重叠。 示例 2:

输入: intervals = \[ \[1,2], \[1,2], \[1,2] ] 输出: 2 解释: 你需要移除两个 \[1,2] 来使剩下的区间没有重叠。 示例 3:

输入: intervals = \[ \[1,2], \[2,3] ] 输出: 0 解释: 你不需要移除任何区间，因为它们已经是无重叠的了。

#### Solution

前面有一道"合并区间"的题目，和这道题有一点类似。这里会使用合并区间提到的"动态规划"方法，也会使用贪心算法解决问题，最后对两种方法进行比较。

Solution1：动态规划。我们同样可以先对区间作处理，例如按照区间左端点的大小进行排序。例如下图：

![image-20220801163323659](https://cdn.jsdelivr.net/gh/wuyun666/chartBed@main/img/image-20220801163323659.png)

动态规划最重要的是要列出状态转移方程，那么我们应该列出什么样的状态转移方程，状态函数是什么呢？首先，这个状态必须要能够被"转移"，我们假设前四个区间的信息如图所示，第五个区间的左边界可以在\[第四个区间的左边界，第五个区间的右边界]这个范围内（也就是\[E,J]）移动。我们可以看到，前四个区间最多保留两个区间，可以是①③，也可以是①④。如果第五个区间的左边界在G~~H内，那么前五个区间最后保留的一定是①③⑤；如果第五个区间的左边界在H~~J内，那么最后可以是①③⑤也可以是①④⑤。可以，当考虑第5个区间时，需要跟前面几个区间作为末尾的情况比较，因此，我们可以用$f\_i$表示：以区间 i为最后一个区间，可以选出的区间数量的最大值。那么状态转移函数为：

$$
f(i)= \max_{j<i \& j的右端点\le i的左端点} {f_j+1}
$$

为什么要保证j的右端点小于等于i的左端点呢？因为如果大于的话，就重叠了呗……

这样的做法，因为情况i可能要同前面i-1个状态比较，所以最后的时间复杂度是$$O(n^2)$$.

Solution2：贪心算法。贪心算法的核心思想是某个问题的最优解包含了子问题的最优解，缩小问题几何即可得到最优解。我们可以从考虑这个问题开始：如何选择剩余不重叠区间的第一个区间/最后一个区间，选出这个区间后，不断在剩下的空间中求第一个区间/最后一个区间即可。

以选择第一个区间为例，如何选择第一个区间呢？假如有如下的区间：

![image-20220801170215877](https://cdn.jsdelivr.net/gh/wuyun666/chartBed@main/img/image-20220801170215877.png)

这个问题中，黑色的被保留，绿色的被遗弃。黑色的第一个区间就是首个区间，容易想到，区间的左端点不是选择的关键，关键是区间的右端点。我们先选择右端点最小的区间，这就是首个区间。然后不断地贪心寻找下一个右端点最小且不会与前面区间重合的区间即可（即left要大于前面选择区间的right）。因为排序后只需要遍历一遍区间集即可，所以时间复杂度是按区间右端点升序排序的时间复杂度即$$O(nlogn)$$。

两种算法都是通过子问题来解决问题的，不同的是贪心算法的子结构总是最优的，动态规划是利用子结构中获取的信息。

#### Code

**Solution1**

```
class Solution6_1 {
public:
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        if (intervals.size() == 0) return 0;
        //1.sort
        sort(intervals.begin(), intervals.end(), [](const auto& e1, const auto& e2) {return e1[0] < e2[0]; });
        //2.dp
        int total = intervals.size();
        vector<int> f(total,1);

        for (int i = 1; i < total; i++)
        {
            for (int j = 0; j < i; j++)
            {
                if (intervals[j][1] <= intervals[i][0])
                    f[i] = max(f[j] + 1,f[i]);
            }
        }
        //3.寻找最大元素
        return total - *max_element(f.begin(), f.end());
    }
};
```

**Solution2**

```
class Solution6_2 {
public:
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        if (intervals.empty()) return 0;
        //1.sort
        sort(intervals.begin(), intervals.end(), [](const auto& e1, const auto& e2) {return e1[1] < e2[1]; });
        //2.choose the first section
        int total = intervals.size();
        int right = intervals[0][1];
        int count = 1;
        //3.check each element
        for (int i = 1; i < total; i++)
        {
            if (intervals[i][0] < right) continue;
            count++;
            right = intervals[i][1];

        }
        return total - count;
    }
};
```

### 7.递增三元组

link:[334. 递增的三元子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/increasing-triplet-subsequence/)

#### Description

给你一个整数数组 nums ，判断这个数组中是否存在长度为 3 的递增子序列。

如果存在这样的三元组下标 (i, j, k) 且满足 i < j < k ，使得 nums\[i] < nums\[j] < nums\[k] ，返回 true ；否则，返回 false 。

示例 1：

输入：nums = \[1,2,3,4,5] 输出：true 解释：任何 i < j < k 的三元组都满足题意 示例 2：

输入：nums = \[5,4,3,2,1] 输出：false 解释：不存在满足题意的三元组 示例 3：

输入：nums = \[2,1,5,0,4,6] 输出：true 解释：三元组 (3, 4, 5) 满足题意，因为 nums\[3] == 0 < nums\[4] == 4 < nums\[5] == 6

#### Solution

递增三元组，比较容易想到的方法是三层嵌套（对应ijk），由于i\<j\<k，所以最外层和最内层循环加起来需要遍历n次，内层循环需要遍历n次的话，总共的时间复杂度是$O(n^2)$。复杂度有点高，可能是有没用到的信息或者做了重复运算。仔细想想，对于每个j，左边存在一个数小于它等价于左边数集的最小值小于它即可，所以我们可以用一个数组leftmin\[n]保存左边集合的最小值(leftmin\[i]表示\[0...i]这i+1个元素的最小值)，这样的话每个j只需要线性的比较时间。同理，再加一个rightmax\[n]数组，两个数组的构造和j的遍历都需要O(n)的时间，所以最后的时间复杂度是O(n),但是这种方法有一点时间开销。

我们可以使用贪心算法，这就需要我们把问题划分为子问题。我们可以从左向右遍历nums\[i],那么每次nums\[i]只需要和前面最小的元素和其之后的次小的元素比较即可，而且要保证这个"次小元素"是能选出的所有次小元素中最小的即可。

那么，我们可以用一个变量来min维护前面i个元素的最小值，用另一个变量secondMin来维护这个"次小元素"即可。如果一次遍历中发现了nums\[i]>secondMin>min，那么返回true；否则返回false；如果nums\[i]是第二大的元素，那么我们把第二小的元素secondMin更新为nums\[i]；如果发现nums\[i]比前面的两个元素都小，那么把min更新为nums\[i]即可。

有人要问，secondMin不用更新吗？不需要的，因为每次遍历都是Nums\[i]同secondMin来比较，如果后面有数大于secondMin，那说明之前的min和secondMin可以和num\[i]构成答案；如果secondMin>nums\[i] > min，那么就把secondMin更新为nums\[i]，这如果以后找出了答案那就是和这次更新后的min和secondMin构成了三元组；同理如果num\[i]最小，只需要更新最小元素即可。下面给出贪心算法的代码:

#### Code

```
class Solution7 {
public:
    bool increasingTriplet(vector<int>& nums) {
        int total = nums.size();
        if (total < 3) return false;
        int min = nums[0], secondMin = INT_MAX;//0,2147483647
        for (int i = 1; i < total; i++)
        {   //1.case 1:nums[i]>sencondMin>min
            if (nums[i] > secondMin) return true;
            //2.case 2:sencondMin>nums[i]>min
            else if (nums[i] > min) secondMin = nums[i];
            //3.case 3:secondMin>min>nums[i]
            else min = nums[i];
        }
        return false;
    }
};
```

### 8.除自身外数组的乘积

link:[238. 除自身以外数组的乘积 - 力扣（LeetCode）](https://leetcode.cn/problems/product-of-array-except-self/)

#### Description

给你一个整数数组 nums，返回 数组 answer ，其中 answer\[i] 等于 nums 中除 nums\[i] 之外其余各元素的乘积 。

题目数据 保证 数组 nums之中任意元素的全部前缀元素和后缀的乘积都在 32 位 整数范围内。

请不要使用除法，且在 O(n) 时间复杂度内完成此题。

示例 1:

输入: nums = \[1,2,3,4] 输出: \[24,12,8,6] 示例 2:

输入: nums = \[-1,1,0,-3,3] 输出: \[0,0,9,0,0]

#### Solution

answer数组的每个元素实际上是n-1个元素的乘积，很容易想到先求出n个元素的和再除以nums\[i]，但是不允许除法。

暴力运算的话显然会有大量的重复运算。我们可以用两个数组分别来保存重复计算的信息，例如left\[i]表示$$\prod_{j=0}^i nums[i]$$。再用right数组表示sum\[i...n]的成绩。这样计算answer\[i]时只需要使用两个数组计算即可。计算answer、求left、求right的时间复杂度都是O(n)，但是空间复杂度却不是常数。

如果要求除了answer数组外只允许线性空间的话，也不复杂。我们利用上面left构造的方法，首先构造$$answer[i] = \prod_{j=0}^{i-1} nums[i]$$，这样的话，每个数只缺少右面的元素然后再进行一次遍历，这次同right数组的构造类似，右边的第一个元素需要乘以1，右边第二个元素需要乘nums\[n-1],第三个元素需要乘nums\[i-1]\*nums\[n-1]。以此类推，只要用一个临时变量来保存nums\[i]应该乘的值，再不断向左更新即可。

#### Code

```
class Solution8 {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int total = nums.size();
        vector<int> answer(total);

        answer[0] = 1;
        for (int i = 1; i < total; i++) {
            answer[i] = nums[i - 1] * answer[i - 1];
        }

        int Right = 1;
        for (int i = total - 1; i >= 0; i--) {
            answer[i] = answer[i] * Right;
            // Right更新，表示左边的下一个元素需要多乘一个元素nums[i]
            Right *= nums[i];
        }
        return answer;
    }
};
```

### 9.和为K的子数组

link:[560. 和为 K 的子数组 - 力扣（LeetCode）](https://leetcode.cn/problems/subarray-sum-equals-k/)

#### Description

给你一个整数数组 nums 和一个整数 k ，请你统计并返回 该数组中和为 k 的连续子数组的个数 。

示例 1：

输入：nums = \[1,1,1], k = 2 输出：2 示例 2：

输入：nums = \[1,2,3], k = 3 输出：2

#### Solution

比较容易想到的当然是枚举了……用一个二维数组用动态规划的方法即可：$$sum[i,j]=\sum^{j}_{k=i} nums[k]$$。

运算中有求和求积的一般挺容易有重复运算的，我们可以思考如何少作重复运算。我们可以用sum\[i]表示前i个元素的和，如：$$sum[i]=\sum^{i}_{k=0} nums[k]$$。

这样的话，我们实际上只要考察是否存在i,j，使得sum\[i]-sum\[j]==k即可。那么在考虑第i个元素的时候，只需要寻找有几个j，能使得sum\[j]的值等于sum\[i]-k。我们可以用一个哈希表来保存sum的出现次数，例如如果sum\[j]=m，那么map\[m]=map\[m]+1，表示和为m的前缀出现次数加一。这样的话对于每个元素i，寻找sum\[j]的时间就是O(1)，从而可以简化时间。这样的算法时间复杂度和空间复杂度都是O(n)。

使用map需要用到头文件 unorder\_map.h。

#### Code

```
class Solution9 {
public:
    int subarraySum(vector<int>& nums, int k) {
        unordered_map<int, int> map;
        map[0] = 1;
        int count = 0, sum = 0;
        for (auto& x : nums) {
            sum += x;
            if (map.find(sum - k) != map.end()) {
                count += map[sum - k];
            }
            map[sum]++;
        }
        return count;
    }
};
```

## 字符串练习

### 1.单词规律

link:[290. 单词规律 - 力扣（LeetCode）](https://leetcode.cn/problems/word-pattern/)

#### Description

给定一种规律 pattern 和一个字符串 s ，判断 s 是否遵循相同的规律。

这里的 遵循 指完全匹配，例如， pattern 里的每个字母和字符串 s 中的每个非空单词之间存在着双向连接的对应规律。

示例1:

输入: pattern = "abba", s = "dog cat cat dog" 输出: true 示例 2:

输入:pattern = "abba", s = "dog cat cat fish" 输出: false 示例 3:

输入: pattern = "aaaa", s = "dog cat cat dog" 输出: false

#### Solution

这个题目的做法挺好想的，关键是以此题为例熟悉一下字符串的常用操作和常用到的数据结构。

根据" " 切割s，pattern中不同的字符不能映射相同的字符串，s中不同的单词也不能映射相同的模式字符。

#### Code

```
class Solution10 {
public:
    bool wordPattern(string pattern, string s) {
        unordered_map<string, char> strTochar;
        unordered_map<char, string>charTostr;
        int total = s.length();
        int i=0;//i is a point for the position of s
        for (auto ch : pattern)
        {   //1. if pattern is longer than s
            if (i >= total) return false;
            string temp = "";
            while (i < total && s[i] != ' ') temp += s[i++];
            i++;//skip " "
            if (strTochar.count(temp) > 0 && strTochar[temp] != ch) return false;
            else strTochar[temp] = ch;
            if (charTostr.count(ch) > 0 && charTostr[ch] != temp) return false;
            else charTostr[ch] = temp;
        }
        // 2.if s is longer than pattern
        return i >= total;
    }
};
```

### 2.划分字母区间

link:[763. 划分字母区间 - 力扣（LeetCode）](https://leetcode.cn/problems/partition-labels/)

#### Description

字符串 S 由小写字母组成。我们要把这个字符串划分为尽可能多的片段，同一字母最多出现在一个片段中。返回一个表示每个字符串片段的长度的列表。

示例：

输入：S = "ababcbacadefegdehijhklij" 输出：\[9,7,8] 解释： 划分结果为 "ababcbaca", "defegde", "hijhklij"。 每个字母最多出现在一个片段中。 像 "ababcbacadefegde", "hijhklij" 的划分是错误的，因为划分的片段数较少。

#### Solution

比较容易想到的方法是，如果把每种字母看成一个区间，区间的左右端点分别是该字母第一次出现的位置和最后一次出现的位置。那么这个问题就等价于：如何把区间集进行最多的划分，使得每个区间落在一个区间子集内，并且各个划分的区间子集互不相交，有点类似于数组的第六题"无重叠子区间"，按照那个题目的方法做即可。

事实上，本题直接用贪心算法做是非常方便的，首先遍历一遍字符串，获取每种字母最后出现的位置，例如字母k最后出现的位置是$end\_k$。然后遍历整个字符串，用end和start记录当前考虑的片段的起始和结束位置，首先令end=start=0，每遇到一个字母，就把结束位置置为max(end,$$end_k$$)，直到遍历到end，便分割出一个片段来，然后令start=end+1，继续考察下一个片段。

上面提到的贪心算法和无重叠子区间的贪心算法有所不同，但是都能做出结果来，这里给出上面提到的贪心算法的代码。

#### Code

```
class Solution11 {
public:
    vector<int> partitionLabels(string s) {
        unordered_map<char, int> endPosition;
        vector<int> result;
        int total = s.length();
        for (int i = 0; i < total; i++)
        {
            endPosition[s[i]] = i;
        }
        int start = 0, end = 0,count=0;
        for (int i = 0; i < total; i++)
        {
            end = max(end, endPosition[s[i]]);
            if (i == end)
            {
                start = end + 1;
                end = end + 1;
                result.push_back(++count);
                count = 0;
            }
            else count++;
        }
        return result;
    }
};
```

### 3.重复的DNA序列

link:[187. 重复的DNA序列 - 力扣（LeetCode）](https://leetcode.cn/problems/repeated-dna-sequences/)

#### Description

DNA序列 由一系列核苷酸组成，缩写为 'A', 'C', 'G' 和 'T'.。

例如，"ACGAATTCCG" 是一个 DNA序列 。 在研究 DNA 时，识别 DNA 中的重复序列非常有用。

给定一个表示 DNA序列 的字符串 s ，返回所有在 DNA 分子中出现不止一次的 长度为 10 的序列(子字符串)。你可以按 任意顺序 返回答案。

示例 1：

输入：s = "AAAAACCCCCAAAAACCCCCCAAAAAGGGTTT" 输出：\["AAAAACCCCC","CCCCCAAAAA"] 示例 2：

输入：s = "AAAAAAAAAAAAA" 输出：\["AAAAAAAAAA"]

#### Solution

最笨的办法就是找到所有长度为10的序列，然后用哈希表来存储出现的次数，输出所有出现次数大于1的字串即可。这种方法的时间复杂度和空间复杂度都是O(10L)，其中L是字符串的长度。

上述算法能否再改进？事实上，哈希表以string作key是比较消耗时间的，而该字符串中只有A、T、C、G四个字母，可以用两位二进制数来表示，例如00为A，01为T，10为C，11为G。这样的话长度为10的字符串就可以用20位的整数来表示，而integer作为key是比较方便的。单单这样不行，这样的话时间复杂度仍然未O(10L)，因为每次都要把10位长度的子串映射为一个数字Integer。如何解决这个问题呢，事实上，相邻两个长度为10的子串中有9位是相同的，我们可以利用这个信息简化运算。

假设前一个长度为10的子串映射为$$x_{old}$$,后一个为$$x_{new}$$,那么$$x_{new}$$的19~~2位就是$x\_{old}$的17~~0位，所以$$x_{new}=x_{old}<<2$$，然后需要把21~~20位置为0，把1~~0位置为新的字符。所以$$x_{new}=(x_{old}]<<2|charToInt(ch)) \& 0xfffff$$.

#### Code

```
class Solution3 {
public:
    vector<string> findRepeatedDnaSequences(string s) {
        const int L = 10;
        unordered_map<char, int> charToInt = { {'A',0b00},{'T',0b01},{'C',0b10},{'G',0b11}};
        unordered_map<int, int> Count;
        vector<string> result;
        int length = s.length();
        if (length < 11) return result;
        int x = 0;
        //first x
        for (int i = 0; i < 10; i++)
        {
            x = charToInt[s[i]]|x<<2;
        }
        Count[x]++;
        for (int i = 10; i<length; i++)
        {
            x = (x << 2 | charToInt[s[i]]) & 0x0fffff;
            if (++Count[x] == 2) result.push_back(s.substr(i-L+1, L));
        }
        return result;
    }
};
```

### 4.最长回文串

link:[5. 最长回文子串 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-palindromic-substring/)

#### Description

给你一个字符串 s，找到 s 中最长的回文子串。

示例 1：

输入：s = "babad" 输出："bab" 解释："aba" 同样是符合题意的答案。 示例 2：

输入：s = "cbbd" 输出："bb"

提示：

1 <= s.length <= 1000，s 仅由数字和英文字母组成

#### Solution

最容易想到的方法就是用双指针i,j列举$$\frac {n(n-1)}{2}$$个子串，然后一一验证，这样的话最坏会有$$O(n^3)$$的时间复杂度，而且显然其中有大量的重复运算。显然，我们可以用数组来保存其中的运算结果，例如，用p\[i,j]来表示子串s\[i...j]是否是回文串，true表示是回文串，false表示否。显然，状态转移方程为：$$p[i,j]=p[i+1,j-1]\&s_i==s_j$$。初始时，我们要进行初始化，显然$$p[i,i]=true,p[i,i+1]=s_i==s_{i+1}$$。然后还要注意动态规划循环时i,j的方向，显然，因为状态转移方程要用到i+1,j-1的信息，所以第一层循环是区间的长度，第二层循环是左边界的位置，才能保证不会出错。

#### Code

```
class Solution4 {
public:
    string longestPalindrome(string s) {
        int length = s.length();
        string result = "";
        bool** p = new bool* [length];
        for (int i = 0; i < length; i++)
        {
            p[i] = new bool[length];
        }
        //memset(p, false, sizeof(p));
        //1.init array
        int maxSubStrLength = 0;
        for (int i = 0; i < length; i++)
        {
            p[i][i] = true;
            maxSubStrLength = 1;
            if (i == 0) result = s[0];
        }
        for (int i = 0; i < length-1; i++)
        {
            p[i][i+1] = s[i]==s[i+1];
            if (s[i] == s[i + 1]) result = s.substr(i, 2);
        }
        //2.dp
        for (int Len = 3; Len <= length; Len++)
        {
            for (int i = 0; i < length&&i + Len - 1<length; i++)
            {
                int j = i + Len - 1;//右边界
                if (s[i] != s[j])
                {
                    p[i][j] = false;
                }
                else
                {   
                    p[i][j] = p[i + 1][j - 1];
                    if (j - i + 1 > maxSubStrLength&&p[i][j])
                    {
                        maxSubStrLength = j - i + 1;
                        result = s.substr(i, maxSubStrLength);
                    }

                }
            }
        }
        //3.release memory and return string
        for (int i = 0; i < length; i++)
        {
            delete[] p[i];
            p[i] = NULL;
        }
        delete[] p;
        p = NULL;
        return result;
    }
};
```
