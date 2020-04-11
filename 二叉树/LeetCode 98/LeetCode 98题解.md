# LeetCode 98题解

**题目描述：**

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

**分析：**

显然，这是一个具有完全包含关系的问题，采用递归求解，思路也比较简单，获得左子树最大值，和右子树最小值，判断是否构成二叉搜索树，同时还要判断左右子树是否为二叉搜索树。

**为了防止递归中调用另一个不同的递归函数造成重复计算，使用一个结构体存储一棵树的最大值，最小值和是否为二叉搜索树。**



**代码：**

```c
long max(long a, long b) {
    return a > b ? a : b;
}

long min(long a, long b) {
    return a < b ? a : b;
}

typedef struct Node {
    bool flag;
    long max_n, min_n;
} Node;
// 时间复杂度O(N),空间复杂度O(1)
Node Check(TreeNode *root) {
    if (!root) {
        Node ret = {true, INT64_MIN, INT64_MAX};
        return ret;
    }
    Node left = Check(root->left);
    Node right = Check(root->right);
    Node ret;
    ret.flag = (root->val > left.max_n && root->val < right.min_n) & left.flag & right.flag;
    ret.min_n = min(root->val, min(left.min_n, right.min_n));
    ret.max_n = max(root->val, max(left.max_n, right.max_n));
    return ret;
}

long getMax(TreeNode *root) {
    if (!root) return INT64_MIN;
    long l_max = getMax(root->left);
    long r_max = getMax(root->right);
    return max(root->val, max(l_max, r_max));
}

long getMin(TreeNode *root) {
    if (!root) return INT64_MAX;
    long l_min = getMin(root->left);
    long r_min = getMin(root->right);
    return min(root->val, min(l_min, r_min));
}

bool isValidBST(TreeNode *root) {
    Node ret = Check(root);
    return ret.flag; 
    /*if (!root) return true;
    if (!root->left && !root->right) return true;
    long l_max = getMax(root->left);
    long r_min = getMin(root->right);
    if (root->val > l_max && root->val < r_min) return true & isValidBST(root->left) & isValidBST(root->right);
    return false;
    */
}
```

