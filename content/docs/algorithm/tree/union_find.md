---
title: UnionFind
weight: 2
bookToc: false
---

# UnionFind (Disjoint Set)



{{< tabs "uniqueid" >}}
{{< tab "c++" >}}
```cpp
class UnionFind {
public:
    unordered_map<int, int> uf;

    int find(int a) {
        if(!uf.count(a))
            uf[a] = a;
        if(uf[a] != a)
            uf[a] = find(uf[a]);
        return uf[a];
    }
    void merge(int a, int b) {
        uf[find(b)] = find(a);
    }
};
```
{{< /tab >}}
{{< tab "python" >}}
```py
class UnionFind:
    def __init__(self):
        self.uf = {}

    def union(self, a, b):
        roota = self.find(a)
        rootb = self.find(b)
        self.uf[rootb] = roota

    def find(self, a):
        if a not in self.uf:
            self.uf[a] = a
        if self.uf[a] != a:
            self.uf[a] = self.find(self.uf[a])
        return self.uf[a]
```
{{< /tab >}}