---
layout: post
title: 七种常见的排序算法
category: 算法
tags: 排序 算法 C++
description: 介绍七种常见排序算法的实现
math: true
---

* 目录
{:toc}

<!-- more -->

## 1.冒泡排序

特性：稳定排序 原地排序
思路：从后往前，比较相邻元素，如果靠后的元素小，就执行交换，让较小元素依次上浮。

### 冒泡排序实现

``` cpp
void bubble_sort1(vector<int> &a) {
    auto n = a.size();
    for (decltype(n) i = 0; i < n; ++i)
        for (auto j = i + 1; j < n; ++j)
            if (a[j] < a[i]) swap(a[j], a[i]);
}
```

最坏运行时间：$$O(n^2)$$
最好运行时间：$$O(n^2)$$

### 改进的冒泡排序实现

当内部循环结束但并未执行任何交换，说明整个数组已经有序，可以提前返回。

``` cpp
void bubble_sort2(vector<int> &a) {
    auto n = a.size();
    auto flag = false;
    for (decltype(n) i = 0; i < n; ++i) {
        if (flag) return;
        flag = true;
        for (auto j = n - 1; j > i; --j)
            if (a[j] < a[j - 1]) {
                swap(a[j], a[j - 1]);
                flag = false;
            }
    }
}
```

最坏运行时间：$$O(n^2)$$
最好运行时间：$$O(n)$$

*[完整实现](https://github.com/wfxr/sort/blob/master/bubble_sort.h)*

## 2.选择排序

特性：不稳定排序，原地排序
思路：从第一个元素开始，依次找出剩余数组中最小的元素并执行交换。

### 选择排序实现

``` cpp
void selection_sort(vector<int> &a) {
    auto n = a.size();
    for (decltype(n) i = 0; i < n; ++i) {
        auto min = i;
        for (decltype(n) j = i + 1; j < n; ++j)
            if (a[j] < a[ min ]) min = j;
        swap(a[i], a[min]);
    }
}
```

最坏运行时间：$$O(n^2)$$
最好运行时间：$$O(n^2)$$

选择排序的复杂度和冒泡排序类似，但相对于冒泡排序交换的次数较少（选择排序最多交换$$n$$次）。

*[完整实现](https://github.com/wfxr/sort/blob/master/selection_sort.h)*

## 3.插入排序

特性：稳定排序，原地排序
思路：每次将未排序的元素插入已排序部分的合适位置（与抓取扑克牌的方式一致）。

### 插入排序实现

``` cpp
void insert_sort1(vector<int> &a) {
    auto n = a.size();
    for (decltype(n) i = 1; i < n; ++i)
        for (auto j = i; j > 0 && a[j] < a[j - 1]; --j)
            swap(a[j], a[j - 1]);
}
```

### 改进的插入排序实现

在找到合适位置之前，交换操作不是必要的，改进的插入排序实现避免了额外的交换。

``` cpp
void insert_sort2(vector<int> &a) {
    auto n = a.size();
    for (decltype(n) i = 1; i < n; ++i) {
        auto t = a[i];
        auto j = i;
        for (; j > 0 && t < a[j - 1]; --j)
            a[j] = a[j - 1];
        a[j] = t;
    }
}
```

插入排序比较适用于数据量较小的排序，其复杂度与数组中的逆序对的总数成比例，对于已排序或接近有序的数组，插入排序的效率很高。

最坏运行时间：$$O(n^2)$$
最好运行时间：$$O(n)$$

*[完整实现](https://github.com/wfxr/sort/blob/master/insert_sort.h)*

## 4.希尔排序

特性：不稳定排序，原地排序
思路：基本思路和插入排序一致，但希尔排序通过比较相隔较远的元素，元素的移动可以跨越很长的距离，从而通过一次比较和交换就可能抵消了原本需要多次的比较和交换。

### 希尔排序实现

``` cpp
void shell_sort(vector<int> &a) {
    auto n = a.size();
    for (auto d = n / 2; d >= 1; d /= 2) {
        for (auto i = d; i < n; ++i)
            for (auto j = i; j >= d && a[j] < a[j - d]; j -= d)
                swap(a[j], a[j - d]);
    }
}
```

希尔排序实际上也是插入排序的一种，也叫做缩小增量排序。排序的效率与增量的选取方法有关，一般好于O($$n^2$$))。

最坏运行时间：$$O(n^2)$$
最好运行时间：$$O(n)$$

*[完整实现](https://github.com/wfxr/sort/blob/master/shell_sort.h)*

## 5.归并排序

特性：稳定排序，需要$$O(logn)$$空间
思路：将数组分为两个子数组，然后分别在子数组上递归调用排序算法，两个子数组有序之后，只需要将其合并即可。

### 归并排序实现

``` cpp
using size_type = vector<int>::size_type;

// merge算法的第1种实现
void merge1(vector<int> &a, vector<int> &aux, size_type l, size_type m, size_type u) {
    auto i = l;     // 左侧数组的下标
    auto j = m + 1; // 右侧数组的下标
    auto k = l;     // 辅助数组aux的下标
    while (i <= m && j <= u)
        if (a[j] < a[i])    // if语句的条件关系排序的稳定性
            aux[k++] = a[j++];
        else
            aux[k++] = a[i++];

    while (i <= m)
        aux[k++] = a[i++];
    while (j <= u)
        aux[k++] = a[j++];

    for (auto k = l; k <= u; ++k)
        a[k] = aux[k];
}

// merge算法的第2种实现
void merge2(vector<int> &a, vector<int> &aux, size_type l, size_type m, size_type u) {
    auto i = l;
    auto j = m + 1;
    auto k = l;

    while (k <= u)
        if (i > m)
            aux[k++] = a[j++];
        else if (j > u)
            aux[k++] = a[i++];
        else if (a[j] < a[i])
            aux[k++] = a[j++];
        else
            aux[k++] = a[i++];

    for (k = l; k <= u; ++k)
        a[k] = aux[k];
}

void merge_sort(vector<int> &a, vector<int> &aux, size_type l, size_type u) {
    if (l >= u) return;
    auto m = l + (u - l) / 2;
    merge_sort(a, aux, l, m);
    merge_sort(a, aux, m + 1, u);
    merge1(a, aux, l, m, u);
}

void merge_sort(vector<int> &a) {
    if (a.empty()) return;  // 当a为空时，a.size() - 1会溢出
    vector<int> aux(a.size());
    merge_sort(a, aux, 0, a.size() - 1);
}
```

归并排序是分治法的一个典型应用，是极少的稳定且时间复杂度为O($$nlogn$$)的排序算法。

最坏运行时间：$$O(nlogn)$$
最好运行时间：$$O(nlogn)$$

*[完整实现](https://github.com/wfxr/sort/blob/master/merge_sort.h)*

## 6.快速排序

特性：不稳定排序
思路：先在数组中选定一个支点元素，与其他元素做比较，将小于支点的元素移动到左侧，大于支点的元素移动到右侧，如此支点元素已经置于正确的位置；接着分别对左侧和右侧的子数组递归调用排序算法，最终得到有序的数组。

### 快速排序的简单实现

``` cpp
void quick_sort1(vector<int> &a) {
    quick_sort1(a, 0, a.size() - 1);
}

void quick_sort1(vector<int> &a, int l, int u) {
    if (l >= u) return;
    auto t = a[l];
    auto i = l + 1, j = u;
    while (i <= j)
        if (a[i] < t)
            ++i;
        else
            swap(a[i], a[j--]);

    swap(a[l], a[j]);
    quick_sort1(a, l, j - 1);
    quick_sort1(a, j + 1, u);
}
```

### 更高效的快速排序实现

``` cpp
void quick_sort2(vector<int> &a) {
    quick_sort2(a, 0, a.size() - 1);
}

void quick_sort2(vector<int> &a, int l, int u) {
    if (l >= u) return;
    auto t = a[l];
    auto i = l, j = u + 1;
    for (;;) {
        do ++i; while (i <= u && a[i] < t);
        // 因为j向左移动时必然经过t，此时a[j] > t不成立，循环结束
        do --j; while (a[j] > t);
        if (i > j) break;
        // i指向的是（从前往后）第一个大于t的元素
        // j指向的是（从后往前）第一个小于t的元素
        swap(a[i], a[j]);
    }

    swap(a[l], a[j]);
    quick_sort2(a, l, j - 1);
    quick_sort2(a, j + 1, u);
}
```

### 三向切分的快速排序实现

在元素重复率高的时候，三向切分的快速排序拥有很高的效率。

``` cpp
void quick_sort3(vector<int> &a) {
    quick_sort3(a, 0, a.size() - 1);
}

void quick_sort3(vector<int> &a, int l, int u) {
    if (l >= u) return;
    auto t = a[l];
    auto lt = l, i = l + 1, gt = u;
    while (i <= gt) {
        if (a[i] < t)
            swap(a[i++], a[lt++]);
        else if (t < a[i])
            swap(a[i], a[gt--]);
        else
            ++i;
    }

    // [l, lt)范围内的元素均小于t
    // [lt, gt]范围内的元素均等于t
    // (gt, u]范围内的元素均大于t
    quick_sort3(a, l, lt - 1);
    quick_sort3(a, gt + 1, u);
}
```

最坏运行时间：$$O(n^2)$$
最好运行时间：$$O(nlogn)$$

快速排序也是分治法的一个典型应用，是使用最广泛的排序算法，通常也是最快的排序算法。

*[完整实现](https://github.com/wfxr/sort/blob/master/quick_sort.h)*

## 7.堆排序

特性：不稳定排序，原地排序
思路：利用堆结构的顶部始终是最大（最小）值这一特征进行排序。

### 堆排序实现

``` cpp
void heap_sort1(vector<int> &a) {
    auto n = a.size();
    for (decltype(n) heap_size = 2; heap_size <= n; ++heap_size)
        shift_up(a, heap_size);
    while (n > 1) {
        swap(a[0], a[--n]);
        shift_down(a, n);
    }
}

// 对大小为n的堆中最后一个元素执行上浮操作
void shift_up(vector<int> &a, int n) {
    for (int i = n - 1; i > 0;) {
        auto c = (i - 1) / 2; // i 的父节点
        if (a[i] < a[c]) break;
        swap(a[c], a[i]);
        i = c;
    }
}


// 对大小为n的堆中第i个元素执行下沉操作
void shift_down(vector<int> &a, int n, int i = 0) {
    for (;;) {
        auto c = 2 * i + 1;
        // c是左子节点
        if (c >= n) break;
        // c+1是右子节点
        if (c + 1 < n && a[c] < a[c + 1]) ++c;
        // c现在是左右子节点中较大的那个
        if (a[c] < a[i]) break;
        swap(a[i], a[c]);
        i = c;
    }
}
```

### 改进的堆排序实现

改进的堆排序实现只使用下沉操作来完成排序，效率更高：

``` cpp
void heap_sort2(vector<int> &a) {
    auto n = a.size();
    // 可以通过从倒数第二层开始依次往上执行下沉操作来恢复堆，
    // 因为最下面一层节点没有子节点，已经是堆
    // n / 2 - 1是最后一个节点的父节点，即倒数第二层的最后一个节点
    for (int i = n / 2 - 1; i >= 0; --i)
        shift_down(a, n, i);
    while (n > 1) {
        swap(a[0], a[--n]);
        shift_down(a, n);
    }
}
```

最坏运行时间：$$O(nlogn)$$
最好运行时间：$$O(nlogn)$$


*[完整实现](https://github.com/wfxr/sort/blob/master/heap_sort.h)*
