# LeetCode407 接雨水II 题解

## 一、题目描述

给你一个 `m x n` 的矩阵，其中的值均为非负整数，代表二维高度图每个单元的高度，请计算图中形状最多能接多少体积的雨水。

提示:

+ m == heightMap.length
+ n == heightMap[i].length
+ 1 <= m, n <= 200
+ 0 <= heightMap[i][j] <= 2 * 10^4




## 二、分析

### 1. 「Dijstra变体」 

每个位置能够接的雨水受到从当前位置到边界的路径上的最大高度限制，因此要算出从当前位置到边界的所有路径最大高度的最小值，**换言之，就是找到从边界到每个点的路径中的找到最大高度最小的，可用贪心证明其正确性，使用优先队列，每次从优先队列取出的有效的节点就是该位置到边界的所有路径中最大高度最小的**。

(可以看作Dijstra变体是因为可以认为将**距离定义为每条路径上的最大高度**，其余的和正常的Dijstra一样)

(「从当前位置到边界的所有路径」：因为「木桶原理」，装水受限于所有木板的高度，否则，有一个路径最大高度较小的话，可能会导致水的溢出)



### 2. 并查集

可以将能够装水的部分看作属于同一集合，使用并查集求解应该是可行的「TODO」。



## 三、代码

### 1. 「Dijstra变体」

```c++
struct Node {
    int x, y, h;

    Node() = default;
    Node(int x, int y, int h) : x(x), y(y), h(h) {}

    bool operator<(const struct Node &that) const {
        return this->h > that.h;
    }
};

int dir[4][2] = {1, 0, -1, 0, 0, 1, 0, -1};

int trapRainWater(vector<vector<int>>& heightMap) {
    int m = heightMap.size(), n = heightMap[0].size();
    priority_queue<Node> que;
    for (int i = 0; i < n; i++) que.push(Node(0, i, heightMap[0][i])), que.push(Node(m - 1, i, heightMap[m - 1][i]));
    for (int i = 0; i < m; i++) que.push(Node(i, 0, heightMap[i][0])), que.push(Node(i, n - 1, heightMap[i][n - 1]));
    vector<vector<int>> check(m, vector<int>(n, 0));
    int ans = 0;
    while (!que.empty()) {
        auto temp = que.top();
        que.pop();
        if (check[temp.x][temp.y]) continue;
        check[temp.x][temp.y] = 1;
        ans += temp.h - heightMap[temp.x][temp.y];
        for (int i = 0; i < 4; i++) {
            int x = temp.x + dir[i][0];
            int y = temp.y + dir[i][1];
            if (x < 0 || x >= m || y < 0 || y >= n || check[x][y]) continue;
            que.push(Node(x, y, max(temp.h, heightMap[x][y])));
        }
    }
    return ans;
}
```

 