堆是一种特殊的数据结构，也叫作优先队列，堆有许许多多的演化结构，如treap、双端优先队列等，堆的使用比较复杂，这里用几道例题来尝试理解。

## 1.数组中第k大的数字

link:[剑指 Offer II 076. 数组中的第 k 大的数字 - 力扣（LeetCode）](https://leetcode.cn/problems/xx4gT2/)

### Description

给定整数数组 `nums` 和整数 `k`，请返回数组中第 k 个最大的元素。

请注意，你需要找的是数组排序后的第 `k` 个最大的元素，而不是第 `k` 个不同的元素。

示例 1:

输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
示例 2:

输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4

### Solution

堆排序常考的排序方法，其中的思想方法也非常值得我们学习。这里我们并不需要完全排列这个数组，只需要找出第k大的元素即可，所以我们基于堆排序的方法进行改进，只需要建立一个大根堆，然后去掉k-1个元素就好了。本题有一个时间复杂度为线性的算法：快速选择算法，将在后面的章节"排序"介绍。构建堆有三个主要的过程：添加元素、调整结构、删除元素，创建堆正是基于这些基本操作。关于堆的介绍可以查看这篇文章：[(19条消息) C++数据结构详解——堆_霸道小明的博客-CSDN博客_c++ 堆](https://blog.csdn.net/qq_54169998/article/details/121098648)。

### Code

```c++
class Solution1 {
public:

    void siftHeap(vector<int>& nums, int index, int heapSize)
    {
        int left = index * 2 + 1, right = index * 2 + 2;
        int largest = index;
        //compare with left and right,select the largest element in [index,left,right]
        if (left<heapSize && nums[left]>nums[largest])
            largest = left;
        if (right < heapSize && nums[right] > nums[largest])
            largest = right;
        if (largest != index) 
        {
            swap(nums[largest], nums[index]);
            siftHeap(nums, largest, heapSize);
        }
    }

    void buildHeap(vector<int>& nums, int heapSize)
    {
        for (int i = heapSize / 2; i >= 0; i--)
        {
            siftHeap(nums, i, heapSize);
        }
    }

    int findKthLargest(vector<int>& nums, int k) {
        int heapSize = nums.size();
        buildHeap(nums, heapSize);
        for (int i = nums.size() -1; i >= nums.size() +1-k; i--)
        {
            swap(nums[0], nums[i]);
            heapSize--;
            siftHeap(nums, 0, heapSize);
        }
        return nums[0];
    }
};
```

## 2.最接近原点的K个点

link:[973. 最接近原点的 K 个点 - 力扣（LeetCode）](https://leetcode.cn/problems/k-closest-points-to-origin/)

### Description

给定一个数组 points ，其中$ points[i] = [x_i, y_i]$ 表示 X-Y 平面上的一个点，并且是一个整数 k ，返回离原点 (0,0) 最近的 k 个点。

这里，平面上两点之间的距离是 欧几里德距离（ √(x1 - x2)2 + (y1 - y2)2 ）。

你可以按 任何顺序 返回答案。除了点坐标的顺序之外，答案 确保 是 唯一 的。

示例 1：

![img](https://assets.leetcode.com/uploads/2021/03/03/closestplane1.jpg)

输入：points = [[1,3],[-2,2]], k = 1
输出：[[-2,2]]
解释： 
(1, 3) 和原点之间的距离为 sqrt(10)，
(-2, 2) 和原点之间的距离为 sqrt(8)，
由于 sqrt(8) < sqrt(10)，(-2, 2) 离原点更近。
我们只需要距离原点最近的 K = 1 个点，所以答案就是 [[-2,2]]。

### Solution

本题与上一道题大同小异，在实际的解题中，我们可以使用c++提供的优先队列的api来使用大根堆或小根堆，本题尝试使用该api来解题。我们可以构建

### Code

```c++
class Solution2 {
public:
    vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
        //cmp function for heap
        struct cmp { bool operator()(pair<int, int> a, pair<int, int> b) { return a.first > b.first; } };
        priority_queue<pair<int,int>,vector<pair<int, int>>,cmp> heap;//<distance^2,index>
        int num = points.size();
        
        for (int i = 0; i < num; i++)
        {
            int distance = points[i][0] * points[i][0] + points[i][1] * points[i][1];
            heap.emplace(distance, i);
        }
        vector<vector<int>> result = {};
        for (int i = 0; i < k; i++)
        {
            result.push_back(points[heap.top().second]);
            heap.pop();
        }
        return result;
    }
};
```



## 扩展题：double queue

link:[3481 -- Double Queue (poj.org)](http://poj.org/problem?id=3481)

### Description

Description

The new founded Balkan Investment Group Bank (BIG-Bank) opened a new office in Bucharest, equipped with a modern computing environment provided by IBM Romania, and using modern information technologies. As usual, each client of the bank is identified by a positive integer *K* and, upon arriving to the bank for some services, he or she receives a positive integer priority *P*. One of the inventions of the young managers of the bank shocked the software engineer of the serving system. They proposed to break the tradition by sometimes calling the serving desk with the **lowest** priority instead of that with the highest priority. Thus, the system will receive the following types of request:

| 0         | The system needs to stop serving                             |
| --------- | ------------------------------------------------------------ |
| 1 *K* *P* | Add client *K* to the waiting list with priority *P*         |
| 2         | Serve the client with the highest priority and drop him or her from the waiting list |
| 3         | Serve the client with the lowest priority and drop him or her from the waiting list |

Your task is to help the software engineer of the bank by writing a program to implement the requested serving policy.

Input

Each line of the input contains one of the possible requests; only the last line contains the stop-request (code 0). You may assume that when there is a request to include a new client in the list (code 1), there is no other request in the list of the same client or with the same priority. An identifier *K* is always less than 106, and a priority *P* is less than 107. The client may arrive for being served multiple times, and each time may obtain a different priority.

Output

For each request with code 2 or 3, the program has to print, in a separate line of the standard output, the identifier of the served client. If the request arrives when the waiting list is empty, then the program prints zero (0) to the output.

Sample Input

```
2
1 20 14
1 30 3
2
1 10 99
3
2
2
0
```

Sample Output

```
0
20
30
10
0
```

### Solution

本题看似复杂，但是实际上只需要能够找到当前最小优先级的节点最高优先级的节点即可。容易想到的是可以用两个堆，一个大跟堆一个小根堆来维护。数组的话就不要想了，肯定会超时。这里可以使用双端堆或者treap来解决问题。两种数据结构c++都没有提供api，只能自己实现，而且实现起来确实挺复杂的。

```c++
#include<iostream>
#include<algorithm>
#include<stdlib.h>
using namespace std;
/*
* 本题应当采用treap的数据结构
* treap的本质是数据用二叉平衡树获取，采用随机数来维护堆
* 但是因为直接采用二叉链表的方法会导致超时，所以将数据结构改为使用索引数组
*/
#define MAX 500010
struct Node {
	int K;
	int P;
	int random;
	int left, right;	//利用值是否为0来判断是否是叶子节点
	int size;
	int countK;
} nodes[MAX];
int index = 0;
int root;

int getNewNode(int k, int p) {
	nodes[++index].K = k;
	nodes[index].P = p;
	nodes[index].random = rand();
	nodes[index].countK = nodes[index].size = 1;
	return index;
}
void addSize(int no) {
	nodes[no].size = nodes[nodes[no].left].size + nodes[nodes[no].right].size + nodes[no].countK;
}
void init() {
	getNewNode(0 ,-0x3f3f3f3f);	//1
	getNewNode(0, 0x3f3f3f3f);	//2
	root = 1;
	nodes[1].right = 2;
	addSize(1);					//1是父节点
}
void rotateRight(int &node) {
	int temp = nodes[node].left;
	nodes[node].left = nodes[temp].right;
	nodes[temp].right = node;
	node = temp;
	//必须从下向上计算size，否则会出错
	addSize(nodes[node].right);
	addSize(node);
}
void rotateLeft(int& node) {
	int temp = nodes[node].right;
	nodes[node].right = nodes[temp].left;
	nodes[temp].left = node;
	node = temp;
	addSize(nodes[node].left);
	addSize(node);
}
void insert(int& node, int k, int p) {
	if (!node) {
		node = getNewNode(k, p);
		return;
	}
	if (nodes[node].P == p)nodes[node].countK++;
	else if (p < nodes[node].P) {
		insert(nodes[node].left, k, p);
		if (nodes[nodes[node].left].random > nodes[node].random) rotateRight(node);
	}
	else
	{
		insert(nodes[node].right, k, p);
		if (nodes[nodes[node].right].random > nodes[node].random) rotateLeft(node);
	}
	addSize(node);	//重新计算node的size值
}
void remove(int& node, int p) {
	if (!node) return;
	if (nodes[node].P == p) {
		if (nodes[node].countK > 1) nodes[node].countK--;
		else if (nodes[node].left == 0 && nodes[node].right == 0) {
			node = 0;
		}
		else
		{
			if (nodes[node].left == 0 || nodes[nodes[node].right].random > nodes[nodes[node].left].random) {
				rotateLeft(node);
				remove(nodes[node].left, p);	//原先的结点到了左分支上
			}
			else
			{
				rotateRight(node);
				remove(nodes[node].right, p);	//右旋后原先的节点到了右分支上
			}
		}
	}
	else if (nodes[node].P > p) remove(nodes[node].left, p);
	else remove(nodes[node].right, p);
	addSize(node);
}
int getPbyPos(int node, int pos) {
	if (!node) return 0x3f3f3f3f;
	if (nodes[nodes[node].left].size >= pos) return getPbyPos(nodes[node].left, pos);
	else if (nodes[nodes[node].left].size + nodes[node].countK >= pos) {
		cout << nodes[node].K << endl;
		return nodes[node].P;
	}
	else return getPbyPos(nodes[node].right, pos - nodes[nodes[node].left].size - nodes[node].countK);
}

int main() {
	index = 0;
	init();
	int op;
	int totalNum = 2;
	int k, p;
	while (cin >> op,op) {
	if (op == 1) {
			cin >> k >> p;
			insert(root, k, p);
			totalNum++;
		}
		else if (op == 2) {
			if (totalNum == 2)cout << 0 << endl;
			else
			{
				p = getPbyPos(root, totalNum-1);
				remove(root, p);
				totalNum--;
			}
		}
		else
		{
			if (totalNum == 2)cout << 0 << endl;
			else
			{
				p = getPbyPos(root, 2);
				remove(root, p);
				totalNum--;
			}
		}
	}
	return 0;
}
```

