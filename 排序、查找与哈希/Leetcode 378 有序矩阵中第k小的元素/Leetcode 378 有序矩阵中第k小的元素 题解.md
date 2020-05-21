# Leetcode 378题解

**题目描述：**给定一个 *n x n* 矩阵，其中每行和每列元素均按升序排序，找到矩阵中第k小的元素。

**分析：**这是查找类型的问题，由于这是矩阵，最直接的思路就是将矩阵中的元素拷贝到一个数组中，然后对这个数组进行排序，之后获得结果。

但是很明显这不是最优解法，最优解法待自己思考(必要时看别人的题解)。

**代码：**

```c++
void merge(int *num, int l, int r) {
    int mid = (l + r) >> 1, temp[r - l + 1];
    int x = l, y = mid + 1, i = 0;
    while (x <= mid || y <= r) {
        if (x <= mid && (y > r || num[x] < num[y])) temp[i++] = num[x++];
        else temp[i++] = num[y++];
    }
    memcpy(num + l, temp, sizeof(int) * (r - l + 1));
}

void merge_sort(int *num, int l, int r) {
    if (l == r) return;
    int mid = (l + r) >> 1;
    merge_sort(num, l, mid);
    merge_sort(num, mid + 1, r);
    merge(num, l, r);
}

//leetcode上遇到二维数组时函数参数的含义：
// int **matrix:二维数组指针
// int matrixSize：二维数组行数
// int *matrixColsize：二维数组列数(指针类型，通过取值运算符获取)
int kthSmallest(int** matrix, int matrixSize, int* matrixColSize, int k){
    int temp[matrixSize * (*matrixColSize)], cnt = 0;
    for (int i = 0; i < matrixSize; i++) {
        for (int j = 0; j < *matrixColSize; j++) temp[cnt++] = matrix[i][j];
    }
    merge_sort(temp, 0, matrixSize * (*matrixColSize) - 1);
    return temp[k - 1];
}
```



## 杨氏矩阵的二分答案解法

考虑到杨氏矩阵的性质，每一行，每一列从左到右都是递增的，因此在查找第k小元素的时候可以使用二分答案的方法，二分的判定标准是小于等于当前数字的元素个数。

同时，关于**杨氏矩阵，从左到右，每一列中小于等于某一数字的个数是递减的，画图的话就是一个递减的阶梯图。**

```c++
int check(vector<vector<int>> &matrix, int x) {
    int n = matrix.size(), cnt = 0;
    int j = n;
    // 氏矩阵，从左到右，每一列中小于等于某一数字的个数是递减的，这样判定过程的时间复杂度就变成了O(n + m)，这里是O(n)
    for (int i = 0; i < n; i++) {
        while (j && matrix[j - 1][i] > x) --j;
        cnt += j;
    }
    return cnt;
}

int kthSmallest(vector<vector<int>>& matrix, int k) {
    int n = matrix.size();
    int l = matrix[0][0], r = matrix[n - 1][n - 1];
    int mid, ret;
    while (l < r) {
        // 二分答案，二分的对象是数字，判定标准是杨氏矩阵中小于等于当前数字的元素个数
        mid = (l + r) >> 1;
        ret = check(matrix, mid); 
        if (ret < k) l = mid + 1;
        else r = mid;
    }
    return l;
}
```



