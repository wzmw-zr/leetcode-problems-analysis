# Leetcode 110题解

**题目描述：**

给定一个二叉树，判断它是否是高度平衡的二叉树。

**分析：**

这题也是一道具有完全包含关系性质的问题，直接思路是计算当前节点左右子树高度判断是否平衡，但是这样的话会有大量的重复计算，因此可以在计算高度的过程中实现比较两个子树的高度，以此来减少后面的计算。



**代码：**

```c++
typedef struct TreeNode TreeNode;

int Depth(TreeNode *root) {
    if (!root) return 0;
    int l = Depth(root->left);
    int r = Depth(root->right);
    // 如果子树失衡或者本节点失衡，那么就返回-2(-1也是可以的)
    // 判断是否失衡：左右子树是否失衡，当前节点是否失衡
    if (l == -2 || r == -2 || abs(l - r) > 1) return -2;
    return (l > r ? l : r) + 1;
}

bool isBalanced(struct TreeNode* root){
    return Depth(root) >= 0;
}
```

