---
title: Binary Index Tree
bookToc: false
---

# Binary Index Tree (Fenwick Tree)

{{< tabs "uniqueid" >}}
{{< tab "c++" >}}
```cpp
using LL = long long;

class BIT {
private:
    vector<int> tree;
    int n;
public:
    BIT(int n_){
        tree = vector<int>(n_, 0);
        n = n_;
    }
    void update(int idx, int delta) {
        for(idx = idx+1; idx<n; idx+=idx&(-idx)) 
            tree[idx]+=delta;
    }
    LL prefixSum(int idx) {
        LL sum = 0;
        for(idx = idx+1; idx>0; idx-=idx&(-idx))
            sum += tree[idx];
        return sum;
    }
};
```
{{< /tab >}}