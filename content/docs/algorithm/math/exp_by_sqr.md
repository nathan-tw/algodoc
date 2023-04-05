---
title: Exponentiating By Squaring
bookToc: false
---


# Exponentiating By Squaring (快速冪)

把次方視為二進位，每次檢查 lsb 是否為 1，是的話多乘一次，不是的話 base 以次方成長。

```c++
int power(int base, int exp) {
    int result = 1;
    while (exp > 0) {
        if (exp & 1)
            result *= base;
        base *= base;
        exp >>= 1;
    }
    return result;
}
```