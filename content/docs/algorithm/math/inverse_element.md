---
title: Inverse Element
bookToc: false
---

# Inverse Element

反元素是指在運算中使原本運算還原度元素，在小學學過的負數和倒數其實就是加法和乘法的反元素，這裡談的則是模下乘法反元。
模反元素的定義是，在模 n 的情況下 ab相乘後取模為 1，可以表達成：

{{< katex  >}}
ab\equiv 1{\pmod  {n}}
{{< /katex >}}

也可以寫成：

{{< katex >}}
a^{{-1}}\equiv b{\pmod  {n}}
{{< /katex >}}


整數 a 對模數 n 之模反元素存在的充分必要條件是 a 和 n 互質，若此模反元素存在，在模數 n 下的除法可以用和對應模反元素的乘法來達成，此概念和實數除法的概念相同。

## 如何求模反元素

由上可知，不是所有元素都存在模反元素。那什麼情況會用到模反元素呢？許多排列組合相關的問題因為答案過大，題目通常要求對 1e9+7 取模，在排列組合的公式中，常會出現除以某階乘的運算，此時就需要用到反元素，因為在同餘的性質中並不包含除法的分配率，也就是

{{< katex >}}
\dfrac{a}{b} \% c \ne \dfrac{a\%c}{b\% c}
{{< /katex >}}

對此可以乘以分母的模反元素達到一樣的效果，找反元素的方法有三種：


### **擴展歐幾里德法**

{{< katex display>}}
ax+ny=1
{{< /katex >}}

原理和求線性方程一樣，在模 n 下

{{< katex display>}}
ax+ny \equiv ax \equiv 1
{{< /katex >}}

因此求出一組 x, y 後 x 即為 a 的模反元素。
```c++ 
void exgcd(int a, int b, int& x, int& y) {
    if (b == 0) {
        x = 1, y = 0;
        return;
    }
    exgcd(b, a % b, y, x);
    y -= a / b * x;
}
```
### **快速冪法**
透過費馬小定理可以列出，費馬小定理可以用 (a+1)^n 以二項式展開證明看看。

{{< katex >}}
ax \equiv 1 \pmod b \\
ax \equiv a^{b-1} \pmod b \\
x \equiv a^{b-2} \pmod b
{{< /katex >}}

{{< hint info>}}
用擴展歐幾里德法前提是 a, b 互質，而費馬小定理的前提則是 b 為質數。
{{< /hint >}}


```c++ 
inline int qpow(long long a, int p) {
  int b = 1;
  a = (a % p + p) % p;
  for (int i = p-2; i; i >>= 1) {
    if (i & 1) ans = (a * ans) % p;
    a = (a * a) % p;
  }
  return b;
}
```

### **線性求逆元**

常遇到需要求多個數字的模反元素，此時是可以透過線性的時間算出 n 個數字的反元素。

{{< katex >}}
i^{-1} \equiv \begin{cases}
    1,                                           & \text{if } i = 1, \\
    -\lfloor\frac{p}{i}\rfloor (p \bmod i)^{-1}, & \text{otherwise}.
\end{cases} \pmod p

{{< /katex >}}


```c++ 
inv[1] = 1;
for (int i = 2; i <= n; ++i) {
  inv[i] = (long long)(p - p / i) * inv[p % i] % p;
}
```

 
 
