# Leetcode 102题解

**题目描述：**

给定一个二叉树，返回其按层次遍历的节点值。 

**分析：**

思路一：使用队列对二叉树进行层次遍历即可。(很复杂)

思路二：**使用前序遍历处理与层相关的问题(统计层数，获取一层的节点个数，获取一层的节点元素等)。**在部分情况下，使用二叉树层序遍历的性质与结果的性质，==**可以在基于前序遍历过程的函数中传递层数这样的参数达到对于二叉树每一层的信息的统计和曾内数据处理的功能，可以极大地化简代码。**==

**使用能够前序遍历过程，传递层数作为参数来进行层内信息统计与数据处理，达到与层序遍历处理等价的功能的原因：==前序遍历过程中，同一层的节点的处理顺序是从左往右处理的，这个顺序是和层序遍历处理的顺序是一样的==。所以在前序遍历中传入层数作为参数是可以保证同一层中节点的处理顺序是从左往右的，与层序遍历对于同一层节点的处理顺序是相同的。**



**易错点与难点：**

1. 函数中的参数的含义，Leetcode的一个弊端吧。参数含义在代码中解释。
2. 在进行层序遍历时，这里要求记录每层的节点数量，所以采用了`cur_cnt`和`next_cnt`参数记录当前层的节点数和下一层的节点数，**注意在更新`cur_cnt`时，`next_cnt`要重新置为0！！！**



**代码一：基于前序遍历过程传递层数参数来统计层内信息与进行层内数据数据处理：**

```c++
typedef struct TreeNode TreeNode;

// 获取层数
int getDepth(TreeNode *root) {
    if (!root) return 0;
    int l = getDepth(root->left);
    int r = getDepth(root->right);
    return 1 + (l > r ? l : r);
}

// 统计每一层的节点个数，使用前序遍历，传入层数作为参数以达到对于层内信息的统计与数据处理的功能
void getCnt(TreeNode *root, int k, int *cnt) {
    if (!root) return ;
    cnt[k]++;
    getCnt(root->left, k + 1, cnt);
    getCnt(root->right, k + 1, cnt);
}

// 获取每一层的节点数据，也是使用前序遍历，传入层数作为参数以达到对于曾内信息的统计与数据处理的功能
void getResult(TreeNode *root, int k, int *cnt, int **ret) {
    if (!root) return ;
    ret[k][cnt[k]++] = root->val; 
    getResult(root->left, k + 1, cnt, ret);
    getResult(root->right, k + 1, cnt, ret);
}

int** levelOrder(struct TreeNode* root, int* returnSize, int** returnColumnSizes){
    int depth = getDepth(root);
    int **ret = (int **) calloc(sizeof(int *), depth);
    int *cnt = (int *) calloc(sizeof(int), depth);
    getCnt(root, 0, cnt);
    for (int i = 0; i < depth; i++) {
        ret[i] = (int *) calloc(sizeof(int), cnt[i]);
        cnt[i] = 0;
    }
    *returnSize = depth;
    *returnColumnSizes = cnt;
    getResult(root, 0, cnt, ret);
    return ret;
}
```



**层次遍历代码：**

```c++
typedef struct TreeNode TreeNode;

typedef struct queue {
    struct TreeNode **data;
    int head, tail, size;
} Queue;

Queue *NewQueue(int size) {
    Queue *q = (Queue *) malloc(sizeof(Queue));
    // 空间开大一点，防止不够用
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

// returnSize是一个数字指针表示返回的二维数组的行数，returnColumnSizes是一个整型数组指针，这个需要自己申请空间，存放每行的节点数量
int** levelOrder(struct TreeNode* root, int* returnSize, int** returnColumnSizes){
    int cnt = count_node(root);
    int h = height(root);
    *returnSize = h;
    int **ret = (int **) malloc(sizeof(int *) * h);
    *returnColumnSizes = (int *) malloc(sizeof(int) * h);
    for (int i = 0; i < h; i++) ret[i] = (int *) malloc(sizeof(int) * cnt);
    Queue *q = NewQueue(cnt);
    int cur_cnt = 0, next_cnt = 0, layer = 0;
    push_queue(q, root);
    cur_cnt++;
    while (!empty(q)) {
        int ind = 0;
        (*returnColumnSizes)[layer] = cur_cnt; 
        while (cur_cnt--) {
            if (front(q)->left) {
                push_queue(q, front(q)->left);
                next_cnt++;
            }
            if (front(q)->right) {
                push_queue(q, front(q)->right);
                next_cnt++;
            }
            ret[layer][ind++] = front(q)->val;
            pop(q);
        }
        // 一定要注意这里的更新操作，next_cnt一定要置为0,不然就会发生段错误
        cur_cnt = next_cnt;
        next_cnt = 0;
        layer++;
    }
    return ret;
}

```

