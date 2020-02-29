# Leetcode 107题解

**题目描述：**

给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

**分析：**

还是二叉树的层次遍历，这种情况只需要根据书高对每层获取的数据重新定位就行，大体上还是和Leetcode 102题相同，也是有两种解题方案。



**DFS传递层数参数以统计层内信息与数据处理：**

```c++
typedef struct TreeNode TreeNode;

int getDepth(TreeNode *root) {
    if (!root) return 0;
    int l = getDepth(root->left);
    int r = getDepth(root->right);
    return 1 + (l > r ? l : r);
}

void getCnt(TreeNode *root, int k, int *cnt) {
    if (!root) return ;
    cnt[k]++;
    getCnt(root->left, k - 1, cnt);
    getCnt(root->right, k - 1, cnt);
}
// 逐层处理，而且将结果按层数倒着存储也是可以的，这里的层数正常是真实的层数，另一种就是这种倒转类型
void getResult(TreeNode *root, int k, int *cnt, int **ret) {
    if (!root) return ;
    ret[k][cnt[k]++] = root->val;
    getResult(root->left, k - 1, cnt, ret);
    getResult(root->right, k - 1, cnt, ret);
}

int** levelOrderBottom(struct TreeNode* root, int* returnSize, int** returnColumnSizes){
    int n = getDepth(root);
    int **ret = (int **) calloc(sizeof(int *), n);
    int *cnt = (int *) calloc(sizeof(int), n);
    getCnt(root, n - 1, cnt);
    for (int i = 0; i < n; i++) {
        ret[i] = (int *) calloc(sizeof(int), cnt[i]);
        cnt[i] = 0;
    }
    getResult(root, n - 1, cnt, ret);
    *returnSize = n;
    *returnColumnSizes = cnt;
    return ret;
}
```





**BFS代码：**

```c++
typedef struct TreeNode TreeNode;

typedef struct queue {
    struct TreeNode **data;
    int head, tail, size;
} Queue;

Queue *NewQueue(int size) {
    Queue *q = (Queue *) malloc(sizeof(Queue));
    q->data = (struct TreeNode **) malloc(sizeof(struct TreeNode *) * 2 *size); 
    q->head = q->tail = 0;
    q->size = 2 * size;
    return q;
}

TreeNode *NewTreeNode(int val) {
    TreeNode *t = (TreeNode *) malloc(sizeof(TreeNode));
    t->val = val;
    t->left = t->right = NULL;
    return t;
}

int empty(Queue *q) {
    return q->head == q->tail;
}

void push_queue(Queue *q, struct TreeNode *node) {
    if (q->tail == q->size) return ;
    q->data[q->tail++] = node;
}

struct TreeNode *front(Queue *q) {
    return q->data[q->head];
}

void pop(Queue *q) {
    if (!q) return ;
    if (empty(q)) return ;
    q->head++;
}

void clearQueue(Queue *q) {
    if (!q) return ;
    free(q->data);
    free(q);
}

int count_node(struct TreeNode *root) {
    if (!root) return 0;
    return 1 + count_node(root->left) + count_node(root->right);
}

int height(struct TreeNode *root) {
    if (!root) return 0;
    int h_l = height(root->left);
    int r_l = height(root->right);
    return 1 + (h_l > r_l ? h_l : r_l);
}


int** levelOrderBottom(struct TreeNode* root, int* returnSize, int** returnColumnSizes){
    int cnt = count_node(root);
    int h = height(root);
    *returnSize = h;
    int **ret = (int **) malloc(sizeof(int *) * h);
    *returnColumnSizes = (int *) malloc(sizeof(int) * h);
    for (int i = 0; i < h; i++) ret[i] = (int *) malloc(sizeof(int) * cnt);
    Queue *q = NewQueue(cnt);
    int cur_cnt = 0, next_cnt = 0, layer = 1;
    push_queue(q, root);
    cur_cnt++;
    while (!empty(q)) {
        int ind = 0;
        // 倒着记录就行，h-layer就是倒着存储时第layer层节点数的下标
        (*returnColumnSizes)[h-layer] = cur_cnt; 
        while (cur_cnt--) {
            if (front(q)->left) {
                push_queue(q, front(q)->left);
                next_cnt++;
            }
            if (front(q)->right) {
                push_queue(q, front(q)->right);
                next_cnt++;
            }
            ret[h - layer][ind++] = front(q)->val;
            pop(q);
        }
        cur_cnt = next_cnt;
        next_cnt = 0;
        layer++;
    }
    return ret;
}
```

