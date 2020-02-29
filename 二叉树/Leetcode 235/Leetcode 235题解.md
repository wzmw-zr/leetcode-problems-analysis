# Leetcode 235题解

**题目描述：**

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

**分析：**

分三种情况：（1）根节点值都大于两节点（2）根节点值都小于两节点（3）根节点值在中间

**代码：**

```c++
struct TreeNode *__lowestCommonAncestor(struct TreeNode *root, struct TreeNode *p, struct TreeNode *q) {
    if (rioot->val == p->val) return root;
    if (root->val == q->val) return root;
    if (root->val > p->val && root->val < q->val) return root;
    if (root->val < p->val) return __lowestCommonAncestor(root->right, p, q);
    return __lowestCommonAncestor(root->left, p, q);
}

struct TreeNode* lowestCommonAncestor(struct TreeNode* root, struct TreeNode* p, struct TreeNode* q) {
    if (!root) return NULL;
    if (p->val == q->val) return q;
    if (p->val > q->val) {
        struct TreeNode *temp = p;
        p = q;
        q = temp;
    } 
    return __lowestCommonAncestor(root, p, q);
}

// 我自己的原来代码
struct TreeNode* lowestCommonAncestor(struct TreeNode* root, struct TreeNode* p, struct TreeNode* q) {
    if (root->val > p->val && root->val > q->val) return lowestCommonAncestor(root->left, p, q); 
    if (root->val < p->val && root->val < q->val) return lowestCommonAncestor(root->right, p, q);
    return root;
}
```




