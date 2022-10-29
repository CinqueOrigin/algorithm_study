栈和队列也是很重要的数据结构，在搜索方面将来会发挥重大作用。这里先只摆出两道题，让大家熟悉熟悉用法。

## 1.移除无效的括号

link:[1249. 移除无效的括号 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-remove-to-make-valid-parentheses/)

### Description

给你一个由 '('、')' 和小写字母组成的字符串 s。

你需要从字符串中删除最少数目的 '(' 或者 ')' （可以删除任意位置的括号)，使得剩下的「括号字符串」有效。

请返回任意一个合法字符串。

有效「括号字符串」应当符合以下任意一条要求：

空字符串或只包含小写字母的字符串
可以被写作 AB（A 连接 B）的字符串，其中 A 和 B 都是有效「括号字符串」
可以被写作 (A) 的字符串，其中 A 是一个有效的「括号字符串」


示例 1：

输入：s = "lee(t(c)o)de)"
输出："lee(t(c)o)de"
解释："lee(t(co)de)" , "lee(t(c)ode)" 也是一个可行答案。
示例 2：

输入：s = "a)b(c)d"
输出："ab(c)d"
示例 3：

输入：s = "))(("
输出：""
解释：空字符串也是有效的

### Solution

大家可以模拟一下寻找字符串多余括号的过程。首先如果遇到'('，就把它的位置压入栈中；如果遇到')'且栈中有元素，那么出栈一个元素;如果遇到')'且栈中无元素，那么标记这个位置。最后将栈中的元素一一出栈，并标记其位置。

最后去掉字符串中所有标记过的位置即可。

### Code

```c++
class Solution1 {
public:
    string minRemoveToMakeValid(string s) {
        stack<int> stk;
        string result="";
        bool toDelete[100005];
        int length = s.length();
        memset(toDelete, false,  length* sizeof(bool));

        for (int i = 0; i < length; i++)
        {
            if (s[i] == '(')
            {
                stk.push(i);
            }
            else if (s[i] == ')')
            {
                if (stk.empty())
                {
                    toDelete[i] = true;
                }
                else
                {
                    stk.pop();
                }
            }
        }

        while (!stk.empty())
        {   
            toDelete[stk.top()] = true;
            stk.pop();
        }

        for (int i = 0; i < length; i++)
        {
            if (!toDelete[i]) result += s[i];
        }
        return result;
    }
};
```

## 2.找出游戏的获胜者

link:[1823. 找出游戏的获胜者 - 力扣（LeetCode）](https://leetcode.cn/problems/find-the-winner-of-the-circular-game/)

### Description

共有 n 名小伙伴一起做游戏。小伙伴们围成一圈，按 顺时针顺序 从 1 到 n 编号。确切地说，从第 i 名小伙伴顺时针移动一位会到达第 (i+1) 名小伙伴的位置，其中 1 <= i < n ，从第 n 名小伙伴顺时针移动一位会回到第 1 名小伙伴的位置。

游戏遵循如下规则：

从第 1 名小伙伴所在位置 开始 。
沿着顺时针方向数 k 名小伙伴，计数时需要 包含 起始时的那位小伙伴。逐个绕圈进行计数，一些小伙伴可能会被数过不止一次。
你数到的最后一名小伙伴需要离开圈子，并视作输掉游戏。
如果圈子中仍然有不止一名小伙伴，从刚刚输掉的小伙伴的 顺时针下一位 小伙伴 开始，回到步骤 2 继续执行。
否则，圈子中最后一名小伙伴赢得游戏。
给你参与游戏的小伙伴总数 n ，和一个整数 k ，返回游戏的获胜者。

示例 1：


输入：n = 5, k = 2
输出：3
解释：游戏运行步骤如下：
1) 从小伙伴 1 开始。
2) 顺时针数 2 名小伙伴，也就是小伙伴 1 和 2 。
3) 小伙伴 2 离开圈子。下一次从小伙伴 3 开始。
4) 顺时针数 2 名小伙伴，也就是小伙伴 3 和 4 。
5) 小伙伴 4 离开圈子。下一次从小伙伴 5 开始。
6) 顺时针数 2 名小伙伴，也就是小伙伴 5 和 1 。
7) 小伙伴 1 离开圈子。下一次从小伙伴 3 开始。
8) 顺时针数 2 名小伙伴，也就是小伙伴 3 和 5 。
9) 小伙伴 5 离开圈子。只剩下小伙伴 3 。所以小伙伴 3 是游戏的获胜者。
示例 2：

输入：n = 6, k = 5
输出：1
解释：小伙伴离开圈子的顺序：5、4、6、2、3 。小伙伴 1 是游戏的获胜者。

### Solution

最容易想到的就是模拟这个"出队"的过程了，这里使用队列来模拟。比如示例1的数据，首先我们将五个人按序加入队列，随后我们每次数1个人，就将其出队，然后重新入队。当数到第k个人时，就只将其出队，不再入队。重复上述步骤，直到队列中只剩下一个元素。不熟悉队列的可以先使用这个方法做一下。或者拿一个bool数组循环n-1轮。

因为这里有n个人，所以要一共要排除掉n-1个人。显然这里每次出队的人是几号有规律的。这个数学结构叫做约瑟夫环，感兴趣的话可以了解一下:[【负雪明烛】你能找到的最详细约瑟夫环数学推导！ - 找出游戏的获胜者 - 力扣（LeetCode）](https://leetcode.cn/problems/find-the-winner-of-the-circular-game/solution/by-fuxuemingzhu-laof/)这里不提供这种方法的代码了，虽然这个方法的代码量非常小。

### Code

```c++
class Solution2 {
public:
    int findTheWinner(int n, int k) {
        queue<int> queue;

        for (int i = 1; i <= n; i++)
        {
            queue.emplace(i);
        }
        int temp;
        while (queue.size()>1)
        {
            for (int i = 0; i < k-1; i++)
            {
                queue.emplace(queue.front());
                queue.pop();
            }
            queue.pop();

        }

        temp = queue.front();
        return temp;
    }
};
```

