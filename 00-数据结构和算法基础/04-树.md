

## 1.从前序与中序遍历构造二叉树

link:[105. 从前序与中序遍历序列构造二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

### Description

给定两个整数数组 preorder 和 inorder ，其中 preorder 是二叉树的先序遍历， inorder 是同一棵树的中序遍历，请构造二叉树并返回其根节点。

 ![img](https://assets.leetcode.com/uploads/2021/02/19/tree.jpg)

示例 1:

输入: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
输出: [3,9,20,null,null,15,7]



### Solution

二叉树的前序/后序搭配中序遍历可以唯一确定一颗二叉树，而前序+后序是不可以的。

所谓前序遍历，是指遍历方式为：根节点、左子树、右子树。

所谓中序遍历，是指遍历方式为：左子树、根节点、右子树。

显然该题可以使用递归来完成。

显然，前序遍历的第一个节点就是根节点，可以以此为分界点把中序遍历分为左子树和右子树部分。

想要递归的话，还需要确定这个左子树和右子树在前序遍历中分别是哪两个部分。

方法①：由定义可以，前序遍历中属于左子树部分的最后一个元素是该左子树的最右元素；前序遍历中属于右子树部分的第一个元素是该右子树的根节点。

而中序遍历中属于左子树部分的第一个节点是左子树的最左节点，最后一个节点是属于左子树的最右节点。可见，中序遍历中我们找到的根节点的左边一个元素就是前序遍历中属于左子树部分的最后一个节点，以此来划分前序遍历的部分，并进行递归调用即可。

方法②：该方法更简单，二者的前序遍历和中序遍历的长度是一样的，求出前序遍历的长度就知道了中序遍历的长度，其起始位置也就知道了。

此外，我们可以用一个数据结构来保存"前序遍历中的元素在中序遍历中的位置"，防止重复查找。

## Code

```c++
class Solution1 {
public:

    unordered_map<int, int> preToIn;

    TreeNode* buildTreeFromVectors(vector<int>& preorder, vector<int>& inorder, int preStart, int preEnd, int inStart, int inEnd)
    {
        if (preStart > preEnd) return nullptr;

        TreeNode* root = new TreeNode(preorder[preStart]);

        int divingPointOfInorder = preToIn[preorder[preStart]];
        int leftSubTreeSize = divingPointOfInorder - inStart;

        root->left = buildTreeFromVectors(preorder, inorder, preStart + 1, preStart + leftSubTreeSize , inStart, divingPointOfInorder - 1);
        root->right = buildTreeFromVectors(preorder, inorder, preStart + leftSubTreeSize + 1, preEnd, divingPointOfInorder + 1, inEnd);
       
        return root;
    }

    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int length = inorder.size();
        for (int i = 0; i < length; i++)
        {
            preToIn[inorder[i]] = i;
        }
        return buildTreeFromVectors(preorder, inorder, 0, length - 1, 0, length - 1);
    }
};
```



## 2.最近公共祖先

link:[剑指 Offer 68 - II. 二叉树的最近公共祖先 - 力扣（LeetCode）](https://leetcode.cn/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

### Description

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉树:  root = [3,5,1,6,2,0,8,null,null,7,4]

示例 1:

输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出: 3
解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
示例 2:

输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出: 5
解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。

- 所有节点的值都是唯一的。
- p、q 为不同节点且均存在于给定的二叉树中。

### Solution

该题是一道非常经典的搜索例题。

如果本题二叉树的形式是以数组的形式给出的，例如 root = [3,5,1,6,2,0,8,null,null,7,4]，寻找o、q的最近公共祖先，又该如何查找呢？我们可以不断寻找p的祖先节点，并将其加入到一个map中，使得map[该祖先节点]=1；然后不断寻找q的祖先节点，并查询map[该祖先节点]是否为1，如果为1那么这就是我们要找的公共祖先了，如果不是那么就继续向上查找q的祖先节点。这样我们可以在O(logn)的时间内求出。

但是本题给出的二叉树不是以数组的形式组织的，因此不能直接自底向上查找。但是我们可以以递归的方式遍历一遍树，并建立父节点映射数组或map，然后再查找。构建的时间是O(n)，所以时间复杂度也是O(n)。

还有一种方法，我们可以尝试使用递归的方法来求解该问题。假设函数lowestCommonAncestor(TreeNode *root, TreeNode *p, TreeNode *q)可以在以root为根节点的子树下找到p或者q。那么如果在root的左子树和右子树中lowestCommonAncestor函数都有返回值，因为p和q的值一定不同，那么说明p和q的公共祖先就是root；如果p或q正好就是root，又能从左子树或右子树中的一个中找到q或p，那么说明root是最近公共子祖先。在上面所述的左子树/右子树寻找过程中，本身也可以查找最近公共子祖先。最后考虑递归出口，如果root的为null，说明到了叶节点也没找到，返回没找到即可。下面给出这种方法的代码

### Code

```c++
class Solution2 {
public:
    TreeNode* ans;
    bool dfs(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (root == nullptr ) return false;
        bool lson = dfs(root->left, p, q);
        bool rson = dfs(root->right, p, q);
        if ((lson && rson) || ((root->val == p->val || root->val == q->val) && (lson || rson))) {
            ans = root;
        } 
        return lson || rson || (root->val == p->val || root->val == q->val);
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        dfs(root, p, q);
        return ans;
    }
};

```

## 3.二叉树的锯齿形层序遍历

link:[二叉树的锯齿形层序遍历](https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/)

### Description

给你二叉树的根节点 root ，返回其节点值的 锯齿形层序遍历 。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。

 

示例 1：


输入：root = [3,9,20,null,null,15,7]
输出：[[3],[20,9],[15,7]]
示例 2：

输入：root = [1]
输出：[[1]]
示例 3：

输入：root = []
输出：[]

### Solution

我们知道二叉树的层次遍历需要使用队列，然后一个一个输出即可。这里我们也可以采用同样的方式，用一个bool元素记录当前层是应该正序输出还是逆序输出即可。同时，需要一个变量count记录当前层拥有的元素的个数。

### Code

```c++
class Solution3 {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> result;
        queue<TreeNode*> queue;

        if (root == nullptr) return result;

        int size = 1;
        bool postiveOrder = true;
        queue.push(root);
        TreeNode* temp;
        while (size != 0)
        {
            int length = size;
            size = 0;
            vector<int> nums = {};
            for (int i = 0; i < length; i++)
            {
                temp = queue.front();
                if (temp->left != nullptr)
                {
                    queue.push(temp->left);
                    size++;
                }
                if (temp->right != nullptr)
                {
                    queue.push(temp->right);
                    size++;
                }
                nums.push_back(temp->val);
                queue.pop();
            }
            if (!postiveOrder) reverse(nums.begin(),nums.end());
            postiveOrder = !postiveOrder;
            result.push_back(nums);
        }
        return result;
    }
};
```

## 4.删除二叉搜索树的节点

link:[[450. 删除二叉搜索树中的节点 - 力扣（LeetCode）](https://leetcode.cn/problems/delete-node-in-a-bst/)](https://leetcode.cn/problems/binary-tree-right-side-view/)

### Description

给定一个二叉搜索树的根节点 root 和一个值 key，删除二叉搜索树中的 key 对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。

一般来说，删除节点可分为两个步骤：

首先找到需要删除的节点；
如果找到了，删除它。


示例 1:![img](https://assets.leetcode.com/uploads/2020/09/04/del_node_supp.jpg)



输入：root = [5,3,6,2,4,null,7], key = 3
输出：[5,4,6,2,null,null,7]
解释：给定需要删除的节点值是 3，所以我们首先找到 3 这个节点，然后删除它。
一个正确的答案是 [5,4,6,2,null,null,7], 如下图所示。
另一个正确答案是 [5,2,6,null,4,null,7]。

### Solution

首先要明确二叉搜索树有什么性质：二叉搜索树任意节点的左子树中的节点的值均小于该节点，右子树中的节点的值均大于该节点。

二叉搜索树中删除一个元素有两个步骤：首先要查找到该元素的位置，然后再改变该树的结构使其仍然为一颗二叉搜索树。

删除一个节点时，如果是叶子节点，那么可以直接删除。如果不是叶子节点，就需要考虑一个元素来替代它了。

若删除的节点如果只有左子树，那么可以用该左子树的根节点来代替删除的节点。

若删除的节点如果只有右子树，那么可以用该左子树的根节点来代替删除的右节点。

如果删除的节点同时拥有左右子树，那么我们要用什么节点来代替呢？

![image-20220814163004328](https://cdn.jsdelivr.net/gh/wuyun666/chartBed@main/img/image-20220814163004328.png)

如图，假若要删除值为10的节点，那么应该是从左右子树中寻找一个最接近该节点的节点，然后替换掉值为10的节点。最接近10的节点，应该是左子树的最右节点（左子树中值最大的元素）或者右子树的最左节点（右子树中值最小的元素）。用这两个节点替换原节点后，可以保证其左子树的节点均小于它，右子树的节点均大于它。因为这两个节点一定是叶子节点，所以对子树也没有任何影响。

函数deleteNode(root,key)的作用是在以root为根节点的树中删除值为key的元素，并返回删除该节点后的新的树的结构。

### Code

```c++
class Solution4 {
public:

    TreeNode* deleteNode(TreeNode* root, int key) {
        //1. find the element whose value = key
        if (root == nullptr) return nullptr;
        if (root->val == key)
        {
            if (root->left == nullptr && root->right == nullptr)
            {
                return nullptr;
            }
            else if (root->left == nullptr)
            {
                root = root->right;
            }
            else if (root->right == nullptr)
            {
                root = root->left;
            }
            else
            {   //find the biggest element in left sub-tree
                TreeNode* substitle = root->left;
                while (substitle->right != nullptr)
                    substitle = substitle->right;
                //delete the leaf node
                root->left = deleteNode(root->left, substitle->val);
                substitle->right = root->right;
                substitle->left = root->left;
                return substitle;
            }
        }
        else if (root->val > key)
        {
            root->left = deleteNode(root->left, key);
        }
        else
        {
            root->right = deleteNode(root->right, key);
        }

        return root;
    }
};
```

