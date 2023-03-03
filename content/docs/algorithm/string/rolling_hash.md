---
title: Rolling Hash
bookToc: false
---



# Rolling Hash


Rolling Hash 是一個字段比對方式，利用移動視窗計算視窗內的 hash 值，將推移視窗的複雜度從 O(n) 降到 O(1)。
做法是將字母視作一個多項式的係數，例如 a~z 對應 1~26，而這個多項式就是 hash function，以 **banana** 這個字串來說，要找到 **ana** 這個子字串該怎麼做？

1. 計算 **ana** 的 hash 值 `h`，此時我們需要假設一個質數 `x`，這裡假設 `x=31` ， 計算出 **ana** 的 `h` 為1551。

{{< katex  display>}}
H(x) = x^2+19x+1
{{< /katex >}}

2. 移動長度3的視窗，並同時確認 hash 值是否與 `h` 相同。每次移動視窗需要執行三個步驟：
    1. 將多項式同乘 base。
    2. 將多項式最後一項 (a0) 加上去。
    3. 將最高次剪去。

    根據上述步驟，從 **ban** 轉移到 **ana** 的過程是：
    1. **ban** 的 hash值為1972，將多項式乘以 `x` 得到 61132。
    2. 加上 `'a'-'a'+1`，得到61133。
    3. 將 `x^3`(59582) 減去，得到1551。
    
另外 hash 很容易因為過大而溢位，實作上通常會對一個夠大的質數取餘，例如我常用的 `1e9+7`。

[Leetcode 28](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/description/) 就是最基本的子字串比對，下面是我用 rolling hash 實作解答。
{{< tabs "uniqueid" >}}
{{< tab "c++" >}}
```c++
class Solution {
public:
    int strStr(string haystack, string needle) {
        int n = haystack.length(), m = needle.length();
        long long base = 31, mod = 1e9+7, highest_deg_base = 1, hash = 0, target = 0;
        for(int i = m-1; i >= 0; i--) {
            target = (target + highest_deg_base*(needle[i]-'a')) % mod;
            highest_deg_base = (highest_deg_base * base) % mod;
        }
        for(int i = 0; i < n; i++) {
            hash = (hash % mod * base) % mod;
            hash += haystack[i]-'a';
            if(i >= m)
                hash = (hash - highest_deg_base * (haystack[i-m]-'a') + mod) % mod;
            if(i >= m-1) {
                if(hash == target) 
                    return i-m+1;
            }
        }
        return -1;
    }
};
```
{{< /tab >}}