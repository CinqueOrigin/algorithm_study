# 双指针

归并排序也是很经典的排序方法，在做双指针的题目之前大家可以去了解一下。

## 1.平方数之和

link:[633. 平方数之和 - 力扣（LeetCode）](https://leetcode.cn/problems/sum-of-square-numbers/)

### Description

给定一个非负整数 `c` ，你要判断是否存在两个整数 a,b使得$a^2+b^2=c$.

**示例 1：**

```
输入：c = 5
输出：true
解释：1 * 1 + 2 * 2 = 5
```

**示例 2：**

```
输入：c = 3
输出：false
```

### Solution

枚举$\frac{\lfloor\sqrt c\rfloor(\lfloor\sqrt c\rfloor-1)}{2}$种情况当然可以解决问题，但是有大量的重复运算。使用双指针解决，i指针首先指向0，j指针指向$\lfloor\sqrt c\rfloor$，然后比较$temp=i^2+j^2$,如果temp与c相等返回true；如果temp较大那么j--；如果c较大那么i++。

为什么双指针不会错过值呢？还记得第一章数据结构中数组连续的问题"搜索二维矩阵"吗？[240. 搜索二维矩阵 II - 力扣（LeetCode）](https://leetcode.cn/problems/search-a-2d-matrix-ii/)如果把i看成行号，把j看成列号，c看成要寻找的元素，二维矩阵的元素$matrix[i,j]=i^2+j^2$，是不是就和那个问题变得一样了呢？

这个就涉及到双指针的本质了，二维矩阵实际上可以枚举所有可能的组合，直接遍历二维数组是一个$O(n^2)$的寻找过程，但是因为这个二维数组的有序性，我们可以每次排除掉一行或一列，从而简化寻找的时间复杂度，减少不必要的运算。因为代码和

搜索网二维矩阵很像，这里就不给出了。

## 2.环形链表

link:[141. 环形链表 - 力扣（LeetCode）](https://leetcode.cn/problems/linked-list-cycle/description/)

### Description

给你一个链表的头节点 head ，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。注意：pos 不作为参数进行传递 。仅仅是为了标识链表的实际情况。

如果链表中存在环 ，则返回 true 。 否则，返回 false 。

示例 1：

输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
示例 2：

输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
示例 3：

输入：head = [1], pos = -1
输出：false
解释：链表中没有环。

### Solution

最容易想到的建一个哈希表，如果遍历的过程中一个元素出现了两次，那就说明有环。

这里双指针能够解决问题，但是此双指针并非上面提到过的双指针，是一种全新的方法：快慢指针，它使用的是Floyd判圈法的思想，是链结构的常用方法。下面这篇文章介绍了Floyd判圈算法：

[Floyd判圈算法 - zxzhang - 博客园 (cnblogs.com)](https://www.cnblogs.com/ZhaoxiCheung/p/7355369.html#:~:text=Floyd判圈算法 (Floyd Cycle Detection Algorithm)，又称龟兔赛跑算法 (Tortoise,and Hare Algorithm)，是一个可以在有限状态机、迭代函数或者链表上判断是否存在环，求出该环的起点与长度的算法。 该算法据高德纳称由美国科学家罗伯特·弗洛伊德发明，但这一算法并没有出现在罗伯特·弗洛伊德公开发表的著作中。 如果有限状态机、迭代函数或者链表上存在环，那么在某个环上以不同速度前进的2个指针必定会在某个时刻相遇。 同时显然地，如果从同一个起点 (即使这个起点不在某个环上)同时开始以不同速度前进的2个指针最终相遇，那么可以判定存在一个环，且可以求出二者相遇处所在的环的起点与长度。)

具体思想是，我们定义一快一慢两个指针，慢指针每次移动一步，快指针每次移动两步，如果在移动的过程中，快指针追上了慢指针，那就说明存在环路。

### Code

```c++
class Solution1 {
public:
    bool hasCycle(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return false;//到达尾部
        ListNode* slow = head, * fast = head->next;//因为下面的判断条件是slow是否等于fast,所以初始不能让两个指针一样
        while (slow!=fast)
        {
            if (fast->next == nullptr || fast->next->next == nullptr) return false;
            slow=slow->next;
            fast = fast->next->next;
        }
        //slow == fast
        return true;
    }
};
```

## 3.通过删除字母匹配到字典里最长单词

link:[通过删除字母匹配到字典里最长单词](https://leetcode.cn/problems/longest-word-in-dictionary-through-deleting/)

### DEscription

给你一个字符串 s 和一个字符串数组 dictionary ，找出并返回 dictionary 中最长的字符串，该字符串可以通过删除 s 中的某些字符得到。

如果答案不止一个，返回长度最长且字母序最小的字符串。如果答案不存在，则返回空字符串。

示例 1：

输入：s = "abpcplea", dictionary = ["ale","apple","monkey","plea"]
输出："apple"
示例 2：

输入：s = "abpcplea", dictionary = ["a","b","c"]
输出："a"

### Solution

本题实际上是分别求s在dictionary中的单词中的最长子序列，这里可以使用双指针，一个指向s，一个指向dictionary中的串，再用一个变量记录匹配的字符count即可。另外，最长子序列问题也是经典的动态规划问题，也可以用动态规划的方法解决。

我们可以考虑如何用都动态规划来优化双指针，前面的算法有多少重复运算呢？我们注意到，每个词典中的单词都要与s尝试进行匹配，那么我们可以使用一个数据结构来记录s的结构，使得每个单词与这个结构作比较即可，从而减少重复运算，那么应该用什么数据结构来记录什么呢？在dictionary中的单词与s进行比较时，我们可以用$position[i][j]$来记录**串s在位置i之后字符j第一次出现的位置**，从而每次匹配只需要dictionary中的字符与$position[i][j]$比较即可。状态转移方程显然只能利用i进行转移，如果s[i]=j,那么$position[i][j]=i$，否则$position[i][j]=position[i+1][j]$，因此需要把顺序倒过来进行动态规划，具体的代码如下.

### Code

#### 双指针

```c++
//方法来自https://leetcode.cn/problems/longest-word-in-dictionary-through-deleting/solution/suan-fa-xiao-ai-shuang-zhi-zhen-xiang-ji-6rey/
class Solution {
public:
    bool isSubsequence(string s, string t) {
        int n = s.size(), m = t.size();
        // 如果 t 长度大于 s，一定不是子序列
        if(m > n) return false;
        int i = 0;
        for(char ch : t) {
            while(i < n && s[i] != ch) i++;
            if(i >= n) return false;
            i++;
        }
        return true;
    }

    string findLongestWord(string s, vector<string>& dictionary) {
        // 更长的、字典序更小的排在前面，这样一旦找到，就是最长的、字典序最小的结果
        sort(dictionary.begin(), dictionary.end(), [&](string &a, string &b) {
            if(a.size() == b.size()) return a < b;
            return a.size() > b.size();
        });
        for(string &t : dictionary) {
            if(isSubsequence(s, t)) return t;
        }
        return "";
    }
};
```

#### 动态规划

```c++
class Solution2 {
public:
    string findLongestWord(string s, vector<string>& dictionary) {
        int length = s.length();
        int position[1005][26];
        memset(position, -1, sizeof(position));
        for (int i = length - 1; i >= 0; i--)
        {
            for (int j = 0; j < 26; j++)
            {
                if (s[i] == char(j + 'a')) position[i][j] = i;
                else position[i][j] = position[i + 1][j];
            }
        }

        string result = "";
        for (string word : dictionary)
        {
            int j = 0, i = 0;//j for s,i for word
            int l = word.length();
            for (i = 0; i < l; i++)
            {
                if (position[j][word[i] - 'a'] == -1) break;
                j = position[j][word[i] - 'a'] + 1;
            }
            //match success
            if (i == l)
            {
                if (l > result.length() || (l == result.length() && word.compare(result) < 0))
                    result = word;
            }
        }
        return result;
    }
};
```

