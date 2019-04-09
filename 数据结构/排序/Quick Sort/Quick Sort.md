### 快速排序(Quick Sort)
通过一趟排序将待排记录分隔成独立的两部分，其中一部分记录的关键字均比另一部分的关键字小，则可分别对这两部分记录继续进行排序，以达到整个序列有序。

**算法描述**
快速排序使用分治法来把一个串（list）分为两个子串（sub-lists）。
* 从数列中挑出一个元素，称为 “基准”（pivot）
* 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作
* 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序

**图片演示**
**方法一**
![Quick Sort](https://img-blog.csdn.net/20150810105856630 "Quick Sort")

![Quick Sort](https://img-blog.csdn.net/20150810105908580 "Quick Sort")

![Quick Sort](https://img-blog.csdn.net/20150810105918502 "Quick Sort")

![Quick Sort](https://img-blog.csdn.net/20150810105928634 "Quick Sort")

![Quick Sort](https://img-blog.csdn.net/20150810105938122 "Quick Sort")

![Quick Sort](https://img-blog.csdn.net/20150810105949306 "Quick Sort")

![Quick Sort](https://img-blog.csdn.net/20150810110000025 "Quick Sort")

![Quick Sort](https://img-blog.csdn.net/20150810110009599 "Quick Sort")


**方法二**
![Quick Sort](https://img-blog.csdn.net/20170205232500000?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDg1MzI2MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast "Quick Sort")

**方法三：填坑**
![Quick Sort](https://img-blog.csdn.net/20180707184544296?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbmdsaW5nX25pY2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70 "Quick Sort")

**代码实现**
**方法一**
```
public void quickSort(int[] arr,int low,int high){
    if (low > high) {
        return;
    }

    //一轮排序，获取基准点
    int index = partition(arr, low, high);
    //递归调用左半数组
    quickSort(arr, low, index-1);
    //递归调用右半数组
    quickSort(arr, index+1, high);
}

public int partition(int[] arr,int low, int high) {
    int i=low;
    int j=high;
    int pivot = arr[low];
    int temp;

    while (i < j) {
        //先看右边，依次往左递减
        while (pivot <= arr[j] && i < j) {
            j--;
        }
        //再看左边，依次往右递增
        while (pivot >= arr[i] && i < j) {
            i++;
        }
        //如果满足条件则交换
        if (i < j) {
            temp = arr[j];
            arr[j] = arr[i];
            arr[i] = temp;
        }
    }
    //最后将基准为与i和j相等位置的数字交换
    arr[low] = arr[i];
    arr[i] = pivot;

    return i;
}
```

**方法二、三**
```
public void quickSort(int[] array,int low ,int high){
    if (low >= high) {
        return;
    }
    //进行第一轮排序获取分割点
    int index = partition(array, low, high);
    //排序前半部分
    quickSort(array, low, index - 1);
    //排序后半部分
    quickSort(array, index + 1, high);
}

public static int partition(int[] array, int low, int high){
    //选取了基准点
    int pivot = array[low];
    while(low < high){
        //从后半部分向前扫描
        while (array[high] >= pivot && low < high) {
            high--;
        }
        array[low]=array[high];

        //从前半部分向后扫描
        while (array[low] <= pivot && low < high) {
            low++;
        }
        array[high] = array[low];
    }
    //最后把基准存入
    array[high] = key;
    return high;
}
```

### 参考文档
* https://blog.csdn.net/shujuelin/article/details/82423852
* https://www.cnblogs.com/onepixel/articles/7674659.html
* https://blog.csdn.net/u010853261/article/details/54884784
* https://blog.csdn.net/lingling_nice/article/details/80943231
* https://www.cnblogs.com/chengxiao/p/6194356.html
* https://www.zhihu.com/search?type=content&q=%E5%A0%86%E6%8E%92%E5%BA%8F