### 归并排序(Merge Sort)
归并排序是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。

**算法描述**
* 把长度为n的输入序列分成两个长度为n/2的子序列
* 对这两个子序列分别采用归并排序
* 将两个排序好的子序列合并成一个最终的排序序列

**图片演示**
![Merge Sort](https://images2015.cnblogs.com/blog/1024555/201612/1024555-20161218163120151-452283750.png "Merge Sort")
![Merge Sort](https://images2015.cnblogs.com/blog/1024555/201612/1024555-20161218194508761-468169540.png "Merge Sort")

**代码实现**
```
/* begin = 0; end = arr.length - 1 */

private void MergeSort(int[] arr, int begin, int end) {
    if (begin < end) {
        int mid = (begin + end) / 2;
        //左边归并排序，使得左子序列有序
        MergeSort(arr, begin, mid);
        //右边归并排序，使得右子序列有序
        MergeSort(arr, mid + 1, end);
        //将两个有序子数组合并操作
        merge(arr, begin, mid, end);
    }
}

private void merge(int[] arr, int left, int mid, int right) {
    //左序列指针
    int i = left;
    //右序列指针
    int j = mid + 1;
    //临时数组指针
    int t = 0;
    int[] temp = new int[right - left + 1];

    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) {
            temp[t++] = arr[i++];
        } else {
            temp[t++] = arr[j++];
        }
    }
    //将左边剩余元素填充进temp中
    while (i <= mid) {
        temp[t++] = arr[i++];
    }
    //将右序列剩余元素填充进temp中
    while (j <= right) {
        temp[t++] = arr[j++];
    }
    
    //将temp中的元素全部拷贝到原数组中
    t = 0;
    while (left <= right) {
        arr[left++] = temp[t++];
    }
}
```

### 参考文档
* https://blog.csdn.net/shujuelin/article/details/82423852
* https://www.cnblogs.com/onepixel/articles/7674659.html
* https://blog.csdn.net/u010853261/article/details/54884784
* https://blog.csdn.net/lingling_nice/article/details/80943231
* https://www.cnblogs.com/chengxiao/p/6194356.html
* https://www.zhihu.com/search?type=content&q=%E5%A0%86%E6%8E%92%E5%BA%8F