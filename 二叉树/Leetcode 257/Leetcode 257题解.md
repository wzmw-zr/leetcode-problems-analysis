# Leetcode 257题解

**题目描述：**给定一个二叉树，返回所有从根节点到叶子节点的路径。

**分析：**二叉树的题目通常是具有完全包含关系的，一个二叉树从根节点到叶子节点的所有路径，可以看作是根节点与根节点左右孩子到叶子节点的所有路径的组合，这样就分析出了这道题的完全包含关系。

**易错点与注意点：**

在写字符串时采用了`sprintf`函数，**==使用`sprintf`函数向字符串中写数据时，需要注意写入数据中如果包含数字，注意数字长度，防止数字长度过长导致写入字符串超出空间，报段错误或者`heap-buffer-overflow`==。为了解决这个问题，可以将字符串空间开大，保证有足够的空间存储数字。**

**代码一：**

```c++
// 计算路径数，就是计算叶子节点数量
int count_path(struct TreeNode *root) {
    if (!root->left && !root->right) return 1;
    if (!root->left) return count_path(root->right); 
    if (!root->right) return count_path(root->left);
    return count_path(root->left) + count_path(root->right);
}

//最大深度，在开辟字符串空间时使用
int max_height(struct TreeNode *root) {
    if (!root) return 0;
    int left = max_height(root->left);
    int right = max_height(root->right);
    return 1 + (left > right ? left : right);
}

char ** binaryTreePaths(struct TreeNode* root, int* returnSize){
    // 空节点返回NULL，返回路径数为0
    if (!root) {
        *returnSize = 0;
        return NULL;
    }
    // 叶子节点返回路径数为1, 路径上就只有叶子节点的数据。
    if (!root->left && !root->right) {
        *returnSize = 1;
        char **ret = (char **) malloc(sizeof(char *) * 1);
        ret[0] = (char *) malloc(sizeof(char) * 5);
        sprintf(ret[0], "%d", root->val);
        return ret;
    }
    int left, right;
    char **left_path, **right_path;
    left_path = binaryTreePaths(root->left, &left);
    right_path = binaryTreePaths(root->right, &right);
    int path_cnt = count_path(root), h = max_height(root);
   	// 新建返回的字符串数组，并初始化
    char **ret = (char **) malloc(sizeof(char *) * path_cnt);
    for (int i = 0; i < path_cnt; i++) {
        ret[i] = (char *) calloc(sizeof(char), 10 * h);    
        sprintf(ret[i], "%d->", root->val);
    }
    int i, j;
    // 根据左右孩子返回的路径数组，生成新的返回路径数组
    for (i = 0; i < left; i++) strcat(ret[i], left_path[i]);
    for (int j = 0; j < right; j++) strcat(ret[i + j], right_path[j]);
    *returnSize = left + right;
    return ret;
}
```



**代码二：**

泽哥写这段代码的过程是先写接口函数内的逻辑，之后实现内部的其他功能函数。

```c++
typedef struct TreeNode TreeNode;

//计算路径个数，也就是计算叶子节点个数
int getPathCnt(TreeNode *root) {
    if (!root) return 0;
    if (!root->left && !root->right) return 1;
    return getPathCnt(root->left) + getPathCnt(root->right);
}

// 获得路径实际上也是一个DFS（深度优先搜索）的过程
// 这里还有一个C语言中的小技巧：
// 当我们需要生成一个内容是逐步填充进去的字符串时，我们应当使用 sprintf函数及其返回值和一个len参数来不断更新下一步输入的字符串中位置。同时注意strdup这个函数的使用，会很方便
// 在定义新的功能函数时，考虑需要那些参数时，要大致的想清楚内部的执行逻辑，之后按需确定参数
// 比如这里使用k来表示当前生成的路径在ret中的下标，但是如果是我的话大概率会使用指针参数k
int getResult(TreeNode *root, char **ret, char *buffer, int k, int len) {
    if (!root) return 0;
    len += sprintf(buffer + len, "%d", root->val);
    buffer[len] = 0;
    if (!root->left && !root->right) {
        ret[k] = strdup(buffer);
        return 1;
    }
    len += sprintf(buffer + len, "->");
    int cnt = getResult(root->left, ret, buffer, k, len);
    cnt += getResult(root->right, ret, buffer, k + cnt, len);
    return cnt;
}

// 主要接口函数考虑处理流程逻辑
char ** binaryTreePaths(struct TreeNode* root, int* returnSize){
    int pathCnt = getPathCnt(root);
    char **ret = (char **) calloc(sizeof(char *), pathCnt);
    int max_len = 1024;
    char *buffer = (char *) calloc(sizeof(char), max_len); 
    // 这一步非常关键！！！先在接口函数中考虑到这一步，之后在上面实现，思考需要的参数
    getResult(root, ret, buffer, 0, 0);
    *returnSize = pathCnt;
    return ret;
}
```

