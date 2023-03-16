---
title: Knapsack Problem
bookToc: false
---

# Knapsack Problem

其實背包問題是一個很經典的經濟學問題，在資源有限但慾望無窮的情況下最大收益是多少？想像自己是小偷，你的背包容量有限，但屋子裡的東西價值和重量卻各不相同，每個東西只能拿或不拿，總不能說搬走一半的冰箱吧。這就造成有時候拿一堆體積小低價值的東西會比一個體積大高價值的東西總收益還高。對於這個問題我沒有比較好的學習技巧，就是不停地練習題目訓練觀察的直覺，比較值得一提的是我歸納出幾個常用的下標。

1. 資源：題目通常會給定一些可用資源的陣列，嘗試用此作為下標，作為前 i 個資源對應的狀態。
2. 收益：取得這個收益的狀態，有可能是資源的組合數，關鍵字例如 **how many ways/maximun number of xxx/min cost**

在 leetcode上有許多值得一寫的背包問題，精選了幾題放在這。

- https://leetcode.com/problems/house-robber-ii/
- https://leetcode.com/problems/ones-and-zeroes/
- https://leetcode.com/problems/target-sum/
- https://leetcode.com/problems/shopping-offers/
- https://leetcode.com/problems/2-keys-keyboard/
- https://leetcode.com/problems/minimum-swaps-to-make-sequences-increasing/
- https://leetcode.com/problems/best-team-with-no-conflicts/
- https://leetcode.com/problems/profitable-schemes/
- https://leetcode.com/problems/tallest-billboard/
- https://leetcode.com/problems/pizza-with-3n-slices/
- https://leetcode.com/problems/reducing-dishes/

另外關於解題技巧，dp問題常常有很多不同的形式，有人可能和我一樣遇到的瓶頸，列出了轉移式卻不知道如何下手建表，原因我也不知道哈哈，有可能是對程式語言的不熟悉，我的解決方式是用 top-down dfs + memoization 的方式會比較容易下手 (關鍵字 python lru_cache)，先定址問題，確認自己轉移式沒有錯誤，再來了解 tabulation 的寫法，練習幾題後會更有信心，也才有勇氣面對 leetcode 的茫茫題海不是嗎。

舉個例子 [638.Shopping Offer](https://leetcode.com/problems/shopping-offers/) 想找到滿足needs的最小成本，其實要寫出轉移是很容易：

    目前needs的組合所需的成本 = 上一個消費的商品的的needs組合中轉移到目前狀態成本最小的那個

就是靠著這麼籠統的轉移概念，先寫一個 dfs 版本加上 `@lru_cache`  就過了，簡單粗暴。

{{< tabs "uniqueid" >}}
{{< tab "py" >}}
```py
class Solution:
    def shoppingOffers(self, price: List[int], special: List[List[int]], needs: List[int]) -> int:
        
        @lru_cache    
        def dfs(needs):
            res = float('inf')
            for s in special:
                n = []
                cost = 0
                for i in range(len(needs)):
                    cost += s[i]*price[i]
                    if s[i] > needs[i]:
                        break
                    n.append(needs[i]-s[i])
                if len(n) == len(needs) and cost >= s[-1]:
                    res = min(res, dfs(tuple(n))+s[-1])
            cost = 0
            for i in range(len(needs)):
                cost+=price[i]*needs[i]
            return min(res, cost)

        return dfs(tuple(needs))
```
{{< /tab >}}