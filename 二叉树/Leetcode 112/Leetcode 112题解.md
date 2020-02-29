# Leetcode 112题解

**题目描述：**

给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。



**分析：**

这是一道具有完全包含关系的问题，可以递归求解。(**这也是一个DFS问题，关于路径的搜索题**)。关于树中的搜索题，也是通常具有完全包含关系性质的。



**代码：**

```c++
bool hasPathSum(struct TreeNode* root, int sum){
    if (!root) return false;
    if (!root->left && !root->right) return root->val == sum;
    return hasPathSum(root->left, sum - root->val) || hasPathSum(root->right, sum - root->val);
}
```

