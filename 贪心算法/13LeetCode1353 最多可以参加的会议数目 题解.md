# LeetCode1353 最多可以参加的会议数目 题解

## 一、题目描述

给你一个数组 events，其中 events[i] = [startDayi, endDayi] ，表示会议 i 开始于 startDayi ，结束于 endDayi 。

你可以在满足 startDayi <= d <= endDayi 中的任意一天 d 参加会议 i 。注意，一天只能参加一个会议。

请你返回你可以参加的 最大 会议数目。



## 二、分析

因为每个会议参加一天就行，因此，采用**贪心，每次贪最小会议的结束时间**，这样留给后面参加会议的天数最多。

一开始我还贪了会议的起始时间，发现会议起始时间并没什么大的作用，因为我们**可以在起始时间到终止时间之间任意一天参加会议**，所以采取了**逐时间处理的方式**，需要记录每个时间的开始的会议id，然后遍历到该时间就将这些会议的结束时间加入优先队列，同时将那些终止时间小于当前时间的会议去除。



## 三、代码

```c++
#define MAX_N 100000

int maxEvents(vector<vector<int>>& events) {
    vector<vector<int>> days(MAX_N + 5);
    int n = events.size(), cnt = 0;
    for (int i = 0; i < n; i++) days[events[i][0]].push_back(i);
    priority_queue<int, vector<int>, greater<int>> que;
    for (int i = 1; i <= MAX_N; i++) {
        for (int &ind : days[i]) que.push(events[ind][1]);
        while (!que.empty() && que.top() < i) que.pop();
        if (!que.empty()) {
            que.pop();
            cnt++;
        }
    }
    return cnt;
}
```

