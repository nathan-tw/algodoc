---
title: Topological Sort
bookToc: false
---

# Topological Sort

在一個有向圖中，有 `n` 個節點，`edge` 代表 edge[0] 到 edge[1] 存在路徑，要找到一種路徑可以走完所有的點，可以使用 `Topological Sort`


## DFS
```c++
int n; // number of vertices
vector<vector<int>> adj; // adjacency list of graph
vector<bool> visited;
vector<int> ans;

void dfs(int v) {
    visited[v] = true;
    for (int u : adj[v]) {
        if (!visited[u])
            dfs(u);
    }
    ans.push_back(v);
}

void topological_sort() {
    visited.assign(n, false);
    ans.clear();
    for (int i = 0; i < n; ++i) {
        if (!visited[i]) {
            dfs(i);
        }
    }
    reverse(ans.begin(), ans.end());
}
```


## BFS

1. 建立 adj metrix
2. 計算 in-degree
3. 使用 bfs, 節點加入 queue 的條件是沒有任何 indegree 

```c++
vector<vector<int>> adj(n);
vector<int> degree(n), bfs;
for(vector<int>& e: edges) {
    adj[e[1]].push_back(e[0]);
    degree[e[0]]++;
}

for(int i = 0; i < n; i++)
    if(!degree[i])
        bfs.push_back(i);

for(int i = 0; i < bfs.size(); i++)
    for(int next: adj[bfs[i]]) 
        if(--degree[next] == 0)
            bfs.push_back(next);
if(bfs.size() != n)
    return vector<int>();

return bfs;
```