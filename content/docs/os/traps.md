---
title: Traps
bookToc: false
---

# Traps

有三種情形會造成 CPU 停下手邊的工作，並且強制的轉換到執行另一段程式來處理這些情形，這樣的情形我們統稱為 **Traps**。

* System Call：當呼叫 system call 時會透過 `ecall` 指令請求 kernel 為其做事。
* Exception：當指令非法時，有可能是 user/kernel space 產生，例如除以 0 會嘗試使用非法的記憶體空間會導致 Exception，其實 `ecall` 也是一種產生 Exception 的方式。
* Interrupt：當硬體發出訊號通知 kernel，例如 I/O 完成了讀寫相關的請求。


## Reference

 - https://ithelp.ithome.com.tw/m/articles/10263376