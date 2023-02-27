---
title: Page Table
bookToc: false
---

# Page Table

## Paging Hardware

通常 ISA 操作的是虛擬記憶體，無論是 kernel space 或 user space，因此我們需要一個硬體對應實體記憶體，現代的內存管理單元 (Memory Mangement Unit aka. MMU) 多存在處理器中，

{{< mermaid class="text-center">}}
flowchart LR
CPU --"發出 VA (Virtual Address) "--> 硬體轉換電路 --"發出 MVA (Modified Virtual Address)" --> MMU--"發出 PA (Physical Address)"-->物理內存
{{< /mermaid >}}

## Kernel address space

圖的左邊是 xv6 的內核虛擬記憶體分佈，RWX 分別對應著 PTE (page table entry) 的讀、寫和執行標誌。可以看到 kernel 在實體與虛擬記憶體中同時座落在 `KERNBASE=0x80000000` ，會有這樣直接映射的設計是因為 kernel 中許多位置會頻繁被調用，直接映射可以減少轉換的成本。
{{< figure src="/os/pgtbl/paging.png" width="500" class="text-center">}}

## Physical memory allocation

在執行 runtime 時，實體記憶體需要分配給 page table, user memory, kernel stacks, pipe buffers 空間，例如我們寫 c 語言時常會用到的 `malloc`，實際上會先返回虛擬記憶體給使用者，等到需要用到時才查詢分頁表為空，透過引發 page fault 在缺頁異常中處理實體記憶體分配空間。xv6 的分配空間位於 `PHYSTOP=0x86400000` 至 kernel 之間，每次空間分配與釋放都是以 page 為單位 (`PGSIZE=4096 bytes`)，xv6 透過維護一個 linked list 來確認 page 是否可用。

```c
struct run {
  struct run *next;
};

struct {
  struct spinlock lock;
  struct run *freelist;
} kmem;

```

為了維護一個 `freelist`，kernel 有兩個方法對應到配置與釋放空間：

-   `kalloc`：將 page 移除 linked list

```c
void *
kalloc(void)
{
  struct run *r;

  acquire(&kmem.lock);
  r = kmem.freelist;
  if(r)
    kmem.freelist = r->next;
  release(&kmem.lock);

  if(r)
    memset((char*)r, 5, PGSIZE); // fill with junk
  return (void*)r;
}
```

-   `kfree`：將 page 加入 linked list

```c
void
kfree(void *pa)
{
  struct run *r;

  if(((uint64)pa % PGSIZE) != 0 || (char*)pa < end || (uint64)pa >= PHYSTOP)
    panic("kfree");

  // Fill with junk to catch dangling refs.
  memset(pa, 1, PGSIZE);

  r = (struct run*)pa;

  acquire(&kmem.lock);
  r->next = kmem.freelist;
  kmem.freelist = r;
  release(&kmem.lock);
}
```

## Reference

-   https://www.sifive.com/blog/all-aboard-part-9-paging-and-mmu-in-risc-v-linux-kernel
-   https://ithelp.ithome.com.tw/articles/10267494
-   https://man7.org/linux/man-pages/man2/mlock.2.html
-   https://ithelp.ithome.com.tw/articles/10299498
