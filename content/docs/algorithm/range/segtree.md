---
title: Segment Tree
bookToc: false
---

# Segment Tree

{{< tabs  >}}
{{< tab "c++" >}}
```cpp
class SegTree {
public:
    int n;
    vector<int> tree;
    SegTree(int n_): n(n_) {
        tree = vector<int>(n*4);
    }
    int query(int i, int L, int R, int l, int r) {
        if(l == L && r == R)
            return tree[i];
        int M = (L+R)>>1;
        if(M >= r)
            return query(i*2+1, L, M, l, r);
        else if(M < l)
            return query(i*2+2, M+1, R, l, r);
        return max(query(i*2+1, L, M, l, M), query(i*2+2, M+1, R, M+1, r));
    }
    void modify(int i, int L, int R, int P, int v) {
        if(L==R) {
            tree[i] = max(v, tree[i]);
            return;
        }
        int M = (L+R) >> 1;
        if(M >= P)
            modify(i*2+1, L, M, P, v);
        else modify(i*2+2, M+1, R, P, v);
        tree[i] = max(tree[i*2+1], tree[i*2+2]);
    }
};
```
{{< /tab >}}