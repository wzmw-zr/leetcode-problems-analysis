# Leetcode 297题解

**题目描述：**

序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。

请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。



**分析：**

常见的二叉树的序列化方法（1）广义表（2）Leetcode输入格式的二叉树。

**1.二叉树的广义表序列化与反序列化**

就序列化的效率与空间而言，广义表应当是更为适合的选项，因为==广义表不需要额外使用空间来标识空指针，而且能够包含更为复杂的信息==。同时**由于广义表的结构特性，二叉树转广义表是一个具有完全包含性质的问题，直接使用前序遍历就可以生成广义表**。

而广义表的反序列化则是一个具有显式完全包含关系的问题，使用显式栈或者递归求解都是可以的。

**2.Leetcode输入格式的二叉树**

Leetcode输入格式的二叉树是"[1,2,3,null,null,4,5]"这种格式的，对比二叉树的结构可以发现这是对于二叉树进行层序遍历获得结果，那么显然就需要使用队列。

由于Leetcode格式的序列化二叉树是使用队列进行序列化的，那么反序列化也需要使用队列来进行反序列化的操作。并且根据二叉树的结构特性，反序列化应当是在当前节点出队的时候确定好其子节点。



**代码：**

这里实现的是Leetcode格式的二叉树的序列化与反序列化。

```c++
// 省去队列的结构定义与结构操作
typedef struct TreeNode TreeNode;

TreeNode *NewTreeNode(int val) {
    TreeNode *p = (TreeNode *) calloc(sizeof(TreeNode), 1);
    p->val = val;
    return p;
}
// 计算节点数量是为了确定队列的大小
int node_count(TreeNode *root) {
    if (!root) return 0;
    return 1 + node_count(root->left) + node_count(root->right);
}

/** Encodes a tree to a single string. */
char* serialize(struct TreeNode* root) {
     int cnt = node_count(root), len = 1, num = 0;
     Queue *q = NewQueue(cnt);
     char *ret = (char *) calloc(sizeof(char), 16 * cnt + 5);
     ret[0] = '[';
     TreeNode *p = root;
    push_queue(q, p);
    while (!empty(q)) {
        p = front(q);
        pop_queue(q);
        printf("cnt = %d\n", cnt);
        if (!p) {
            if (!cnt) break;
            if (num) len += sprintf(ret + len, ",null");
            else len += sprintf(ret + len, "null");
            num++;
        } else {
            if (!cnt) break;
            if (num) len += sprintf(ret + len, ",%d", p->val), cnt--;
            else len += sprintf(ret + len, "%d", p->val), cnt--;
            num++;
        }
        if (p) push_queue(q, p->left), push_queue(q, p->right);
    }
    ret[len] = ']';
    return ret;
}

int value(char *str) {
    int ret = 0;
    if (str[0] != '-') {
        for (int i = 0; str[i]; i++) ret = ret * 10 + str[i] - '0';
    } else {
        for (int i = 1; str[i]; i++) ret = ret * 10 + str[i] - '0';
        ret = -ret;
    }
    return ret;
}

/** Decodes your encoded data to tree. */
struct TreeNode* deserialize(char* data) {
    int len = strlen(data), cur_cnt = 1, next_cnt = 0;
    data[len - 1] = '\0';
    data++;
    char *str = strtok(data, ",");
    if (!str) return NULL;
    TreeNode *root = NewTreeNode(value(str)), *p;
    Queue *q = NewQueue(len);
    push_queue(q, root);
    while (!empty(q)) {
        p = front(q);
        pop_queue(q);
        str = strtok(NULL, ",");
        if (!str) return root;
        if (strcmp(str, "null")) {
            p->left = NewTreeNode(value(str));
            push_queue(q, p->left);
            next_cnt++;
        }
        str = strtok(NULL, ",");
        if (!str) return root;
        if (strcmp(str, "null")) {
            p->right = NewTreeNode(value(str));
            push_queue(q, p->right);
            next_cnt++;
        }
        cur_cnt--;
        if (!cur_cnt) cur_cnt = next_cnt, next_cnt = 0;
    }
    return root;
}
```

