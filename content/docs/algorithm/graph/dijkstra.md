---
title: Dijkstra
bookToc: false
---

# Dijkstra


{{< tabs "uniqueid" >}}
{{< tab "c++" >}}
```cpp

int minimumTime(vector<vector<int>>& grid) {
    int n = grid.size(), m = grid[0].size();
    int dirs[5] = {1, 0, -1, 0, 1};
    priority_queue<tuple<int, int, int>> pq;
    pq.push({0, 0, 0}); // level, i, j
    vector<vector<bool>> visited(n, vector(m, false));
    visited[0][0] = true;
    while(!pq.empty()) {
        auto [level, i, j] = pq.top();
        pq.pop();
        level *= -1;
        if(i == n-1 && j == m-1)
            return level;
        for(int d = 0; d < 4; d++) {
            int ni = i+dirs[d];
            int nj = j+dirs[d+1];
            int nlevel = level+1;
            if(min(ni, nj) >= 0 && ni < n && nj < m) {
                if(visited[ni][nj])
                    continue;
                visited[ni][nj] = true;
                if(nlevel >= grid[ni][nj])
                    pq.push({-nlevel, ni, nj});
                else {
                    int diff = grid[ni][nj] - nlevel;
                    pq.push({-(grid[ni][nj]+(diff&1)), ni, nj});
                }
            }
        }
    }
    return -1;
}

```
{{< /tab >}}
