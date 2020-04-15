# LeetCode 315题解

## 题目描述

**[计算右侧小于当前元素的个数](https://leetcode-cn.com/problems/count-of-smaller-numbers-after-self/)**

给定一个整数数组 nums，按要求返回一个新数组 counts。数组 counts 有该性质： counts[i] 的值是  nums[i] 右侧小于 nums[i] 的元素的数量。



## 分析

**这道题实质上是一道==逆序对==题目，解决逆序对数问题，最常用的就是==归并排序==，归并排序中右半部分中的元素进行填入时，就是逆序的出现。**

同时在查看题解的过程中，发现了使用**==二叉排序树==的解法，以此为启发，==二叉排序树与平衡二叉树也可以用于解决排序方面的问题==。**



## 解法汇总

**==LeetCode经典易错点：数组为空的处理。==**

### 1.归并排序求解逆序相关问题

归并排序可以求解逆序相关问题的原理机制：**归并排序是合并两个有序线性表，右侧线性表进行填入时，如果左侧的线性表中还有数据，那么就出现了逆序。**

**归并排序的两大易错点(均在合并的操作中)：**

+ 左右两个有序线性表的数量处理与选择的条件
+ 合并结束进行回拷贝操作是注意`memcpy`是按字节拷贝的，因此需要使用`sizeof`，这一点对于 内存操作函数都是一样的，比如`memset`。

```c
 typedef struct Node {
     // 使用结构体存储一些必要的信息
     // ind：下标，val：元素值，cnt：右侧出现逆序的元素个数
    int ind, val, cnt; 
} Node;

void merge(Node *nums, int left, int right) {
    if (left == right) return ;
    Node tmp[right - left + 1];
    int mid = (left + right) >> 1;
    int x = left, y = mid + 1, ind = 0;
    while (x <= mid || y <= right) {
        // 对于左右有序线性表中元素进行选择的条件，加上等号用来保证数据的有序性，因为数据中可能会出现相同的数字
        if (x <= mid && (y > right || nums[x].val <= nums[y].val)) tmp[ind++] = nums[x++];
        else {
            // 选择右侧的元素，那么左侧还没处理的元素就都找到了一次逆序
            for (int i = x; i <= mid; i++) nums[i].cnt++;
            tmp[ind++] = nums[y++];
        }
    }
    memcpy(nums + left, tmp, sizeof(Node) * (right - left + 1));
}

void merge_sort_and_count(Node *nums, int left, int right) {
    if (left == right) return ;
    int mid = (left + right) >> 1;
    merge_sort_and_count(nums, left, mid);
    merge_sort_and_count(nums, mid + 1, right);
    merge(nums, left, right);
}

int* countS(Node* nums, int numsSize, int* returnSize){
    int *cnt = (int *) calloc(sizeof(int), numsSize);
    *returnSize = numsSize;
    merge_sort_and_count(nums, 0, numsSize - 1);
    for (int i = 0; i < numsSize; i++) {
        cnt[nums[i].ind] = nums[i].cnt;
    }
    return cnt;
}

int* countSmaller(int* nums, int numsSize, int* returnSize){
    *returnSize = numsSize;
    if (numsSize == 0) {
        return NULL;
    }
    Node num[numsSize + 5];
    for (int i = 0; i < numsSize; i++) {
        num[i].ind = i;
        num[i].val = nums[i];
        num[i].cnt = 0;
    }
    int *cnt = countS(num, numsSize, returnSize);
    return cnt;
}
```



### 2.二叉排序树求解

