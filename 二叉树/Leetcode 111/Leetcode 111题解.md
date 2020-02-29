# Leetcode111题解

**题目描述：**

给定一个二叉树，找出其最小深度。最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

**分析：**

这是一个具有完全包含关系性质的问题，使用递归求解。

**易错点：**

注意节点一边有孩子另一边没有孩子的情况，这时该节点不是叶子节点，要选择的是有孩子的一边的深度加1,否则在非叶子节点的情况下，选择左右孩子最小深度的较小值加1.

**代码：**

```c++
int minDepth(struct TreeNode* root){
    if (!root) return 0;
    int l = minDepth(root->left);
    int r = minDepth(root->right);
	if (!l || !r) return (l ? l : r) + 1;
    return 1 + (l > r ? r : l);
}
//下面是优化过的代码，减少了部分计算
int minDepth(struct TreeNode* root){
    if (!root) return 0;
    if (!root->left || !root->right) {
        return minDepth((root->left ? root->left : root->right)) + 1;
    }
    // fmin是math.h中的一个函数，找最小值的
    return fmin(minDepth(root->left), minDepth(root->right)) + 1;
}
```

