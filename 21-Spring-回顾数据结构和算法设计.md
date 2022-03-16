# 回顾数据结构和算法设计

### 算法基础思想

- 实在没辙就模拟
- 枚举
- 递归
- 分治
- DFS/回溯
- BFS
- 贪心
- 排序
- 二分
- 前缀和、差分

## 常用的数据结构

### 链表和数组

可以认为一切数据结构的本质底层结构都是链表或数组

| 特征        | 链表                     | 数组                           |
| --------- | ---------------------- | ---------------------------- |
| 随机访问      | 不支持                    | 支持                           |
| 扩容、插入删除元素 | O(1)                   | O(n)                         |
| 占用空间      | 空间开销更大<br>要存指向下一个数据的指针 | 紧凑的存储在一片连续的存储空间中<br>需要初始开的够大 |

### 树

二叉树

- 前序遍历
- 中序遍历
- 后序遍历
- 层序遍历

堆

- 大根堆
- 小根堆

并查集

### 栈和队列

单调栈

优先队列

### 哈希表

[OI wiki](https://oi-wiki.org/ds/hash/) 的哈希表模板

```c++
struct hash_map {  // 哈希表模板
  struct data {
    long long u;
    int v, nex;
  };  // 前向星结构

  data e[SZ << 1];  // SZ 是 const int 表示大小
  int h[SZ], cnt;

  int hash(long long u) { return u % SZ; }

  int& operator[](long long u) {
    int hu = hash(u);  // 获取头指针
    for (int i = h[hu]; i; i = e[i].nex)
      if (e[i].u == u) return e[i].v;
    return e[++cnt] = (data){u, -1, h[hu]}, h[hu] = cnt, e[cnt].v;
  }

  hash_map() {
    cnt = 0;
    memset(h, 0, sizeof(h));
  }
};
```
