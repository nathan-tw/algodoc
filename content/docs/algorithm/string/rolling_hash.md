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
    
另外 hash 很容易因為過大而溢位，實作上通常會對一個夠大的質數取餘，例如我常用的 `1e9+7`，以下程式碼為了方便說明就沒有取餘數。

{{< tabs "uniqueid" >}}
{{< tab "c++" >}}
```c++
using LL = long long;
string findSubstring(string str, string substr) {
    int n = str.length(), m = substr.length();
    LL base = 31, highest_degree_base = 1, currHash = 0, h = 0;
    
    for(int i = m-1; i >=0; i--) {
        currHash += highest_degree_base * (substr[i]-'a'+1);
        highest_degree_base *= base;
    }
        
    for(int i = 0; i < n; i++) {
        currHash *= base;
        currHash += s[i]-'a'+1;
        if(i >= m)
            currHash -= highest_degree_base * (s[i-len]-'a'+1);
        if(i >= len-1) {
            if(currHash == h)
                return s.substr(i-m+1, len);
        }
    }
    return ""; // not found
};
```
{{< /tab >}}