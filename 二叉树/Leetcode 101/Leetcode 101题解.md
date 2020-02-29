# Leetcode 101题解

**题目描述：**

给定一个二叉树，检查它是否是镜像对称的。

**分析：**

二叉树的题目通常是具有完全包含关系的，这里有两种思路：

思路一：翻转二叉树，之后再做比较。这是在我还没有看出其中完全包含关系时的做法。

思路二：利用**二叉树的完全包含关系的性质，有些时候这种完全包含关系性质可能会需要同时使用两棵子树，并且这两棵子树是混合使用的。**

镜像对称在处理玩根节点之后就转化为了比较两个子树是否镜像对称，这是就需要检查两个字数的根节点，如果根节点相同，那么根据镜像对称的性质，检查左子树的左孩子与右子树右孩子、左子树右孩子与右子树左孩子是否镜像对称。

> ==**解决一些具有完全包含关系的题目可能需要混合处理子问题**==，如混合交叉处理同级的根节点的子树。

**代码：**

```c++
typedef struct TreeNode TreeNode;
bool is_check(TreeNode *p, TreeNode *q) {
    if (p == NULL && q == NULL) return true;
    if (p == NULL || q == NULL) return false;
    if (p->val - q->val) return false;
    // 根据镜像对称的性质，检查左子树的左孩子与右子树右孩子、左子树右孩子与右子树左孩子是否镜像对称。
    return is_check(p->left, q->right) && is_check(p->right, q->left);
}

bool isSymmetric(struct TreeNode* root){
    if (!root) return true;
    // 检查是否镜像对称，就是检查左右子树是否镜像对称
    return is_check(root->left, root->right);
}

```

