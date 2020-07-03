# 排序

## 常考排序

### 快速排序

```c++
template<typename T>
static void QuickSort(T arr[], int len) {
    quickSort(arr, 0, len - 1);
}

template<typename T>
static void quickSort(T arr[], int begin, int end) {
    if (begin >= end) {
        return;
    }
    auto pivot = partition(arr, begin, end);
    quickSort(arr, begin, pivot - 1);
    quickSort(arr, pivot + 1, end);
}

template<typename T>
static int partition(T arr[], int begin, int end) {
    auto base = arr[end];
    auto lessInsert = begin;
    for (int i = begin; i < end; ++i) {
        if (arr[i] < base) {
            swap(arr[lessInsert++], arr[i]);
        }
    }
    swap(arr[lessInsert], arr[end]);
    return lessInsert;
}
```

### 归并排序

```c++
template<typename T>
static void MergeSort(T arr[], int len) {
    auto tmp = new T[len];
    mergeSort(arr, 0, len - 1, tmp);
    delete[] tmp;
}

template<typename T>
static void mergeSort(T arr[], int begin, int end, T tmp[]) {
    if (begin + 1 >= end) {
        return;
    }

    auto mid = begin + (end - begin) / 2;
    auto begin1 = begin;
    auto end1 = mid;
    auto begin2 = mid + 1;
    auto end2 = end;
    mergeSort(arr, begin1, end1, tmp);
    mergeSort(arr, begin2, end2, tmp);

    // merge two parts
    auto index = begin;
    while (begin1 <= end1 && begin2 <= end2) {
        tmp[index++] = arr[begin1] < arr[begin2] ? arr[begin1++] : arr[begin2++];
    }

    while (begin1 <= end1) {
        tmp[index++] = arr[begin1++];
    }

    while (begin2 <= end2) {
        tmp[index++] = arr[begin2++];
    }

    for (int i = begin; i <= end; ++i) {
        arr[i] = tmp[i];
    }
}
```

### 堆排序

用数组表示的完美二叉树 complete binary tree

> 完美二叉树 VS 其他二叉树

![image.png](https://img.fuiboom.com/img/tree_type.png)

[动画展示](https://www.bilibili.com/video/av18980178/)

![image.png](https://img.fuiboom.com/img/heap.png)

核心代码

```c++
template<typename T>
static void HeapSort(T arr[], int len) {
    // right leaf of a root = 2(n + 1) since n is start from 0
    auto lastRoot = len / 2 - 1;
    // 先自下而上构建堆
    for (int i = lastRoot; i >= 0; --i) {
        makeHeap(arr, i, len);
    }
    
    for (int j = len - 1; j >= 0; --j) {
        // 最大值放到后面
        swap(arr[0], arr[j]);
        // 底部都还是有序的，只动了根节点，自上而下更新堆
        makeHeap(arr, 0, --len);
    }
}

template<typename T>
static void makeHeap(T arr[], int root, int len) {
    auto left = root * 2 + 1;
    auto right = (root + 1) * 2;
    auto largest = root;
    if (left < len && arr[left] > arr[largest]) {
        largest = left;
    }
    if (right < len && arr[right] > arr[largest]) {
        largest = right;
    }
    if (largest == root) {
        return;
    }
    swap(arr[largest], arr[root]);
    makeHeap(arr, largest, len);
}
```

## 参考

[十大经典排序](https://www.cnblogs.com/onepixel/p/7674659.html)

[二叉堆](https://labuladong.gitbook.io/algo/shu-ju-jie-gou-xi-lie/er-cha-dui-xiang-jie-shi-xian-you-xian-ji-dui-lie)

## 练习

- [ ] 手写快排、归并、堆排序
