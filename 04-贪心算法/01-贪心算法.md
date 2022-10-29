贪心算法属于是那种听名字很容易理解但是实践起来很难理解的算法。保证每次操作都是局部最优的，并且最后得到的结果是全局最优的。在"数据结构基础"的"数组"部分我们已经有所涉及，如"无重叠区间"的问题。

## 1.根据身高重建队列

link:[406. 根据身高重建队列 - 力扣（LeetCode）](https://leetcode.cn/problems/queue-reconstruction-by-height/description/)

### Description

假设有打乱顺序的一群人站成一个队列，数组 people 表示队列中一些人的属性（不一定按顺序）。每个 people[i] = [hi, ki] 表示第 i 个人的身高为 hi ，前面 正好 有 ki 个身高大于或等于 hi 的人。

请你重新构造并返回输入数组 people 所表示的队列。返回的队列应该格式化为数组 queue ，其中 queue[j] = [hj, kj] 是队列中第 j 个人的属性（queue[0] 是排在队列前面的人）。

示例 1：

输入：people = [[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]
输出：[[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]]
解释：
编号为 0 的人身高为 5 ，没有身高更高或者相同的人排在他前面。
编号为 1 的人身高为 7 ，没有身高更高或者相同的人排在他前面。
编号为 2 的人身高为 5 ，有 2 个身高更高或者相同的人排在他前面，即编号为 0 和 1 的人。
编号为 3 的人身高为 6 ，有 1 个身高更高或者相同的人排在他前面，即编号为 1 的人。
编号为 4 的人身高为 4 ，有 4 个身高更高或者相同的人排在他前面，即编号为 0、1、2、3 的人。
编号为 5 的人身高为 7 ，有 1 个身高更高或者相同的人排在他前面，即编号为 1 的人。
因此 [[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]] 是重新构造后的队列。
示例 2：

输入：people = [[6,0],[5,0],[4,0],[3,2],[2,2],[1,4]]
输出：[[4,0],[5,0],[2,2],[3,2],[1,4],[6,0]]

### Solution

本题的题意是保持原数组people每个元素不变，然后重新给每个元素找位置，使得编号为i的人前面站着的不比TA矮的人正好为people[1]个。

本题既可以先安排个子高的人，也可以先安排个子低的人。这里建议先安排个子低的人，因为先安排个子高的人会使用更多次"插空"操作。这里以先安排矮个子为例。

首先安排刚刚排序好的数组中的第一个元素，因为除了它之外所有的人都不比TA矮，所以它的位置可以直接确定。

然后安排第二个元素，由于**除了之后安排的元素和它之外所有的人都不比TA矮**，所以它的位置也可以直接确定。

后面的元素也是如此。

如果先安排个子最低的人，那么需要首先根据个子进行排序。如果个子相同，该怎么处理呢？可以发现，如果个子相同，那么k值较大的人会被安排在后面。如果先安排k值较小的，在安排k值较大的元素时，不能保证除了之后安排的元素和它之外所有的人都不比TA矮，在考察TA的位置时会考察到刚刚安排的k值较小的元素。如果先安排k值较大的，在安排k值较小的元素时，由于正在安排的元素一定排在刚刚安排的k值较大的元素之前，所以寻找位置时一定不会涉及到刚刚安排的k值较大的元素，在TA考察的区间内等价于除了之后安排的元素和它之外所有的人都不比TA矮，所以各自相同时，应该先安排k较大的。

本题的时间复杂度主要在排序和插入上，排序的时间复杂度是$O(nlogn)$，插入的时间复杂度是$O(n^2)$所以时间复杂度是$O(n^2)$。

### Code

```c++
class Solution1 {
public:
    vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {
        //1.sort
        sort(people.begin(), people.end(), [](const vector<int>& a, const vector<int>& b) {
            return a[0] < b[0] || (a[0] == b[0] && a[1] > b[1]);
            });
        int length = people.size();
        vector<vector<int>> result(length);
        //2.arrange people from low to high
        for (int i = 0; i < length; i++)
        {
            int position = people[i][1] + 1;//there are i people higher than people_i
            int count = 0;//count for the position unarranged before people_i
            for (int j = 0; j < length; j++)
            {
                if (result[j].empty())
                {
                    count++;
                }
                if (count == position)
                {
                    result[j] = people[i];
                    break;
                }
            }
        }
        //3.return 
        return result;
    }
};
```

## 2.买卖股票的最佳时机

link:[121. 买卖股票的最佳时机 - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)

### Description

给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。

你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

示例 1：

输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
示例 2：

输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 没有交易完成, 所以最大利润为 0。



### Solution

本题是要求i,j(i<j),使得price[j]-price[i]最大，最容易想到的就是遍历$O(n^2)$个元素，但是需要做重复运算。实际上，因为每次求这个最大值，是拿当前股票的价格和前面最低的价格比较，所以只需要拿一个变量记录一下该元素之前的最低价格就好，这样只需要一次遍历即可完成任务。

### Code

```c++
class Solution2 {
public:
    int maxProfit(vector<int>& prices) {
        int length = prices.size();
        int lowestPrice=0x3f3f3f3f;
        int maxprofit = 0;
        for (int i = 0; i < length; i++)
        {
            if (lowestPrice >= prices[i]) lowestPrice = prices[i];
            maxprofit = maxprofit > prices[i] - lowestPrice ? maxprofit : prices[i] - lowestPrice;
        }
        return maxprofit;
    }
};
```

## 3.买股票的最佳时机Ⅱ

link:[122. 买卖股票的最佳时机 II - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/description/)

### Description

给你一个整数数组 prices ，其中 prices[i] 表示某支股票第 i 天的价格。

在每一天，你可以决定是否购买和/或出售股票。你在任何时候 最多 只能持有 一股 股票。你也可以先购买，然后在 同一天 出售。

返回 你能获得的 最大 利润 。

示例 1：

输入：prices = [7,1,5,3,6,4]
输出：7
解释：在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6 - 3 = 3 。
     总利润为 4 + 3 = 7 。
示例 2：

输入：prices = [1,2,3,4,5]
输出：4
解释：在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4 。
     总利润为 4 。
示例 3：

输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 交易无法获得正利润，所以不参与交易可以获得最大利润，最大利润为 0 。

### Solution

本题和上一题很像，不同之处在于可以多次购买/售出，并且有了同时最多可持有一张股票的限制。本质上是把上一题的差值换成了多个子区间的差值的和且各个子区间不可相互逾越，实际上就是在求所有的上升子区间，实际上，一个连续的上升子区间可以看作多个长度为2的上升子区间，例如案例输入2中的[1,5]可以看作[1,2],[2,3],……,[4,5]。所以我们可以考察每一个相邻元素的差值，如果差值＞0那么把他加入到收益中。

### Code

```c++
class Solution3 {
public:
    int maxProfit(vector<int>& prices) {
        int length = prices.size();
        int maxprofit = 0;
        for (int i = 1; i < length; i++)
        {
            if (prices[i] - prices[i - 1] > 0) maxprofit += prices[i] - prices[i - 1];
        }
        return maxprofit;
    }
};
```

## 4.判断是否为子序列

link:[392. 判断子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/is-subsequence/)

### Description

给定字符串 s 和 t ，判断 s 是否为 t 的子序列。

字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。

进阶：

如果有大量输入的 S，称作 S1, S2, ... , Sk 其中 k >= 10亿，你需要依次检查它们是否为 T 的子序列。在这种情况下，你会怎样改变代码？

致谢：

特别感谢 @pbrother 添加此问题并且创建所有测试用例。

示例 1：

输入：s = "abc", t = "ahbgdc"
输出：true
示例 2：

输入：s = "axc", t = "ahbgdc"
输出：false



### Solution

字符串的匹配算法是非常经典的贪心算法，这里还用到了动态规划的思想。

首先暴力匹配的算法开始说起，对于暴力匹配算法，如果在某一次匹配中，s中的字符和t中的字符不同，那么s的指针回退到第一个字符的位置，t的指针回退到本次字符串匹配位置的开头的下一个位置，如图所示，这样显然有不必要的运算，显得很呆。

![image-20220823180321778](https://cdn.jsdelivr.net/gh/wuyun666/chartBed@main/img/image-20220823180321778.png)

![image-20220823180404914](https://cdn.jsdelivr.net/gh/wuyun666/chartBed@main/img/image-20220823180404914.png)

暴力算法的时间复杂度是$O(MN)$,其中M、N分别是串s和t的长度。

实际上，对于串s中的第一个字符，我们只需要贪心地寻找串t中第一个和它相同的字符即可，它的位置记为i。对于串s中的第二个字符，我们也只需要贪心地寻找串t中i之后的第一个和它相同的字符即可，以此类推。

所以关键是要获取一个数组，它包含字符ch在串t的位置i之后的下一个字符所在的位置的信息，如$next[ch][i]=j$说明在串t的位置i之后，字符ch下一次出现的位置是j。

这样贪心的匹配，直到串s匹配成功为止，时间开销主要在构建数组上，时间复杂度为O(N)。

### Code

```c++
class Solution4 {
public:
    bool isSubsequence(string s, string t) {
        int s_size = s.size(), t_size = t.size();
        vector<vector<int>> position(t_size + 1, vector<int>(26, 0));

        for (int i = 0; i < 26; i++)
        {
            position[t_size][i] = t_size;
        }

        for (int i = t_size - 1; i >= 0; i--)
        {
            for (int j = 0; j < 26; j++)
            {
                if (t[i] == 'a' + j)
                {
                    position[i][j] = i;
                }
                else
                {
                    position[i][j] = position[i + 1][j];
                }
            }
        }

        int i = 0;
        for (int j = 0; j < s_size; j++)
        {
            if (position[i][s[j] - 'a'] == t_size) return false;
            i = position[i][s[j] - 'a'] + 1;
        }
        return true;
    }
};
```

## 5.非递减数列

link:[665. 非递减数列 - 力扣（LeetCode）](https://leetcode.cn/problems/non-decreasing-array/description/)

### Description

给你一个长度为 n 的整数数组 nums ，请你判断在 最多 改变 1 个元素的情况下，该数组能否变成一个非递减数列。

我们是这样定义一个非递减数列的： 对于数组中任意的 i (0 <= i <= n-2)，总满足 nums[i] <= nums[i + 1]。

示例 1:

输入: nums = [4,2,3]
输出: true
解释: 你可以通过把第一个 4 变成 1 来使得它成为一个非递减数列。
示例 2:

输入: nums = [4,2,1]
输出: false
解释: 你不能在只改变一个元素的情况下将其变为非递减数列。

### Solution

本题的题目要求非常明了，就是尝试更改至多一个数，使之成为"非递减数列"。本题的特色是"面向测试用例"，因为要考虑到各种情况才能过。

对于已经是非递减数列的数组，我们无需更改。

对于其他情况，我们首先要考虑什么样的数列可以经过一次修改变成非递减数列。首先我们要考虑"递减"的部分最多有几处。如果一个数列有两处递减部分，而且这两部分不相邻，那么我们修改任意一处，另一处都是递减的，这种情况只能返回false,例如:

![image-20220827183800041](https://cdn.jsdelivr.net/gh/wuyun666/chartBed@main/img/image-20220827183800041.png)

如果只有这两处递减部分相邻，也就是说存在三个连续的数是递减的，这种情况可否处理呢？也不行，无论修改三个数中的哪一个，第二个数总要和另一个数构成递减，因此，**最多只有一处需要修改**。

如果只有一处构成递减，就一定能够只改一个数就使其变为非递减吗？如下图：

![image-20220827203141468](https://cdn.jsdelivr.net/gh/wuyun666/chartBed@main/img/image-20220827203141468.png)

所以至于能不能，首先考虑发生递减的这两个元素，如上图的10和2，我们可以改变第一个数字，但要保证10改变后要不小于它前面的9，也要不大于后面的2，这显然是不行的，不能保证10后面的元素2大于等于10前面的元素9；或者也可以改变第二个数字，但要保证2改变后要不小于它前面的10，也要不大于它后面的3，这也不能进行修改，因为2前面的10大于2后面的3。所以我们可以对一个数组从前向后遍历，如果发现递减的部分，就分别判断这两个元素的前后元素是否符合修改条件，如果两个修改条件都不符合，那么返回false；如果递减的部分多于2处，也返回false。

### Code

```c++
class Solution {
public:
    bool checkPossibility(vector<int>& nums) {
        int length = nums.size(), count = 0;
        for (int i = 0; i < length-1; i++)
        {   
            int num1 = nums[i], num2 = nums[i + 1];
            if (num1 > num2)
            {
                count++;
                if (count > 1) return false;
               if ((i > 0 && nums[i + 1] < nums[i - 1]) && (i + 2 < length && nums[i] > nums[i + 2])) return false;
            }
        }
        return true;
    }
};
```

