# LeetCode 542题解

## 题目描述

给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。

两个相邻元素间的距离为 1 。

## 分析

**这显然是一道广度优先搜索的问题，本题适合使用==从终点开始进行搜索==，那么就在构造check时遇到0的位置将其加入队列。**

==另外，本题还有动态规划的解法，等复习到动态规划的时候再来补充。==



## 代码

### 1.广度优先搜索

首先，在C++中，**类实际上就是高级的C中的结构体，因此==类是可以作为函数的返回值的。==**

在C++中，如果需要**将类作为函数的参数，一般是采用引用的方式**，C中更多的还是采用指针的方式。

```c++
typedef struct Node {
    int x, y, step;
} Node;

int dir[4][2] = {1, 0, -1, 0, 0, 1, 0, -1};

class Solution {
public:
    // vector<vector<int>>& matrix:C++中传递类的实例的一种方法
    vector<vector<int>> updateMatrix(vector<vector<int>>& matrix) {
        vector<vector<int>> ret, check;
        queue<Node> que;
        int x_max_len = 0, y_max_len = 0;
        // 应对LeetCode中空数组与vector等的情况
        if (matrix.size()) {
            x_max_len = matrix.size();
            if (matrix[0].size()) y_max_len = matrix[0].size();
        }
        // 初始化ret与check，并且处理0的位置
        for (int i = 0; i < matrix.size(); i++) {
            vector<int> tmp, ret_tmp;
            for (int j = 0; j < matrix[i].size(); j++) {
                if (matrix[i][j]) tmp.push_back(0);
                else {
                    tmp.push_back(-1);
                    que.push({i, j, 0});
                }
                ret_tmp.push_back(0);
            }
            ret.push_back(ret_tmp);
            check.push_back(tmp);
        }
        
        while (!que.empty()) {
            Node node = que.front();
            que.pop();
            ret[node.x][node.y] = node.step;
            for (int i = 0; i < 4; i++) {
                int x = node.x + dir[i][0];
                int y = node.y + dir[i][1];
                if (x < 0 || y < 0 || x >= x_max_len || y >= y_max_len) continue;
                if (check[x][y]) continue;
                que.push({x, y, node.step + 1});
                // 一定要记得check的处理
                check[x][y] = 1;
            }
        }

        return ret;
    }
};
```

