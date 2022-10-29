链表是常见也是常考的问题，其数据结构的特殊性也使其需要一些特殊的算法来让我们更高效地使用该数据结构。因为链表这种独特的结构，双指针是链表这种数据结构中常用的方法。

## 1.环形链表

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

## 2.相交链表

link:[160. 相交链表 - 力扣（LeetCode）](https://leetcode.cn/problems/intersection-of-two-linked-lists/)

### Description

给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 null 。

图示两个链表在节点 c1 开始相交：

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)

题目数据 保证 整个链式结构中不存在环。

注意，函数返回结果后，链表必须 保持其原始结构 。

自定义评测：

评测系统 的输入如下（你设计的程序 不适用 此输入）：

intersectVal - 相交的起始节点的值。如果不存在相交节点，这一值为 0
listA - 第一个链表
listB - 第二个链表
skipA - 在 listA 中（从头节点开始）跳到交叉节点的节点数
skipB - 在 listB 中（从头节点开始）跳到交叉节点的节点数
评测系统将根据这些输入创建链式数据结构，并将两个头节点 headA 和 headB 传递给你的程序。如果程序能够正确返回相交节点，那么你的解决方案将被 视作正确答案 。

 

示例 1：

输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
输出：Intersected at '8'
解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,6,1,8,4,5]。
在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。

### Solution

看到两个链表相交，很自然地想到要用双指针，但是如何去用？如果我们每次操作都让两个指针移动一位，那么两个指针在移动的过程中很容易跳过正确答案，如何保证两个指针移动的次数正好使其停留在相交处呢？假设链表A和B的公共部分长度为n，链表A的非公共部分长度为a，链表B的非公共部分长度为b。我们不能保证两个指针各自分别移动a、b次，但是我们可以保证他们移动a+b+c次。如何做到？假设pA指针在链表A中移动，pB指针在链表B中移动，假设a=b，那么pA和pB可以移动a次相交。如果a≠b，那么我们令pA在移动到A的末端时，下一个遍历的元素为链表B的头结点；令pB在移动到B的末端时，下一个遍历的元素为链表A的头结点；这样，pA在移动A链表的长度(a+c)次后，再移动b次达到公共交点；同理pB移动（b+c）+a次到达公共交点，如此保证二者相交。当然，如果a和b没有交点，那么再a遍历完链表B的结点，b遍历完A的结点之后，返回空即可。

### Code

```c++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (headA == nullptr || headB == nullptr) {
            return nullptr;
        }
        ListNode *pA = headA, *pB = headB;
        while (pA != pB) {
            pA = pA == nullptr ? headB : pA->next;
            pB = pB == nullptr ? headA : pB->next;
        }
        return pA;
    }
};
```

## 3.K个一组反转链表

link:[25. K 个一组翻转链表 - 力扣（LeetCode）](https://leetcode.cn/problems/reverse-nodes-in-k-group/)

### Description

给你链表的头节点 head ，每 k 个节点一组进行翻转，请你返回修改后的链表。

k 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

 

示例 1：![img](https://assets.leetcode.com/uploads/2020/10/03/reverse_ex1.jpg)

输入：head = [1,2,3,4,5], k = 2
输出：[2,1,4,3,5]
示例 2：

![img](https://assets.leetcode.com/uploads/2020/10/03/reverse_ex2.jpg)

输入：head = [1,2,3,4,5], k = 3
输出：[3,2,1,4,5]

### Solution

本题的思路很清晰简单，就是每隔k个节点一组反转一次，不够k个节点的那组不需要反转。组内反转的过程也很容易，只是实际代码写起来比较麻烦，要考虑一些边际情况和细节。

首先是要进行分组，如何分组？用一个指针p来进行移动，并记录移动的次数，如果移动的次数为k则分出这k个元素，然后与前面相连接，并继续处理后面的元素。

翻转完一组后，需要将改组与前面的元素相连接，所以需要用一个指针preTail来保存前面一组的尾指针。但是如果链表只有一组可以翻转，那么该组就不需要与前面的组相连，难道要进行特殊情况判断吗？不必，只需要初始时创建一个节点first,使其next值为head即可。在返回头结点时，要返回第一个组的首节点。如果我们把刚刚说的first指针设为第0组的最后一个元素，那么只需要返回first->next即可，不需要额外拿指针保存。

### Code

```c++
class Solution1{
public:
    //1.reverse a list,and return its head node and tail node
    pair<ListNode*, ListNode*> reverseAlist(ListNode* head, ListNode* tail)
    {
        //1.pre for linking two lists
        ListNode* first = tail->next;
        ListNode* second = head;
        //2.reverse
        while (first != tail)
        {
            ListNode* next = second->next;
            second->next = first;
            first = second;
            second = next;
        }
        return { tail, head };
    }
    
    ListNode * reverseKGroup(ListNode * head, int k) {
        //the node before the true head
        ListNode* first = new ListNode(0);
        first->next = head;
        ListNode* preTail = first;
        //head is the first node of the current group,and after move k steps tail represents the end of current group
        while (head != nullptr)
        {
            ListNode *tail = preTail->next;
            //1.check if there are k nodes
            for (int i = 1; i < k; i++)
            {
                tail = tail->next;
                if (tail == nullptr) return first->next;//return 
            }
            //2.reverse the k nodes
            ListNode* nextHead = tail->next;//the head point of the next group
            tie(head, tail) = reverseAlist(head, tail);
            preTail->next = head;
            tail->next = nextHead;
            preTail = tail;
            head = preTail->next;
        }

        return first->next;
    }
};
```

## 4.重排链表

link:[143. 重排链表 - 力扣（LeetCode）](https://leetcode.cn/problems/reorder-list/)

### Description

给定一个单链表 L 的头节点 head ，单链表 L 表示为：

L0 → L1 → … → Ln - 1 → Ln
请将其重新排列后变为：

L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → …
不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

示例 1：

输入：head = [1,2,3,4]
输出：[1,4,2,3]
示例 2：

输入：head= [1,2,3,4,5]
输出：[1,5,2,4,3]

### Solution

很显然，我们可以把列表一分为2，先使得前半部分保持不变，再将后半部分的节点倒序一个一个插入到前半部分中，最终形成结果。

因此，我们首先需要找到链表的中点；然后反转后半部分链表；最后再合并两个链表。

①寻找链表的中点：遍历一遍链表，记录count，然后再从头开始遍历$\lfloor\frac{n}{2}\rfloor$次；或者可以使用快慢指针的方法，快指针每次移动2位，慢指针每次1位。快指针到达尾部的时候慢指针就处于中间位置。这里采用快慢指针的方法。

至于反转链表和合并链表，无需多言。

### Code

```c++
class Solution2 {
public:

    ListNode* findMiddleNode(ListNode* head)
    {
        ListNode* fast = head, * slow = head;
        while (fast->next != nullptr && fast->next->next != nullptr)
        {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }

    ListNode* reverseList(ListNode* head)
    {
        ListNode* first = nullptr, * second = head;
        while (second != nullptr)
        {
            ListNode* temp = second->next;
            second->next = first;
            first = second;
            second = temp;
        }
        return first;
    }

    ListNode* mergeList(ListNode* list1, ListNode* list2)
    {
        ListNode* list1_next, * list2_next,*temp = list1;
        while (list1 != nullptr && list2 != nullptr)
        {
            list1_next = list1->next, list2_next = list2->next;
            list1->next = list2;
            list2->next = list1_next;
            list1 = list1_next;
            list2 = list2_next;
        }

        return temp;
    }

    void reorderList(ListNode* head) {
        ListNode* middle = findMiddleNode(head);
        ListNode* head2 = reverseList(middle->next);
        //split into 2 lists
        middle->next = nullptr;
        head = mergeList(head, head2);
    }
};
```



