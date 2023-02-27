---
title: UnionFind
bookToc: false
---

# UnionFind (Disjoint Set)

Set 在資訊科學中用以代表一個群體，Disjoint Set 也就是互相沒有交集的群體，做法是將群體中的一個元素作為代表，可以透過查找群體中其他成員取得這個元素，通常用來找出圖中的 connected component，也因為有兩個操作分別是 Union, Find 所以稱為 UnionFind。


* Union: 將兩個群體合在一起，很直觀的將 b 原本群體的代表替換成 a 的群體。

        void merge(int a, int b) {
            uf[find(b)] = find(a);
        }

* Find: 如果 a 找不到，代表不屬於任何一個群體，需要新建立一個。如果 a 不是群體代表，則需要向源頭尋找，並且同時做調整。

        int find(int a) {
            if(!uf.count(a))
                uf[a] = a;
            if(uf[a] != a)
                uf[a] = find(uf[a]);
            return uf[a];
        }


將代碼整合後如下：

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