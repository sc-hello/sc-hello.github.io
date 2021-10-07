@[TOC](目录)
# 1 冒泡排序
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201226175647519.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70#pic_center)

## 1.1 代码
```java
public class Bubble {
    // 对数组a中的元素进行排序
    public static void sort(Comparable[] a) {
        for(int i=a.length-1; i>0; i--) {
            for(int j=0; j<i; j++) {
                if (greater(a[j], a[j+1])) {
                    exch(a, j, j+1);
                }
            }
        }
    }

    // 比较v元素是否大于w元素
    private static boolean greater(Comparable v, Comparable w) {
        return v.compareTo(w) > 0;
    }

    // 数组元素i和j交换位置
    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```
## 1.2 复杂性及稳定性分析
 - **时间复杂度**：O(n^2)；
 - **空间复杂度**：O(1)；
 - **稳定性**：稳定；

# 2 选择排序
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201226175735582.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70#pic_center)

## 2.1 代码

```java
public class Selection {
    // 对数组a中的元素进行排序
    public static void sort(Comparable[] a){
        for(int i=0; i<a.length-1; i++) {
        	//假定本次遍历，最小值所在索引是i
            int minIndex = i;
            for(int j=i+1; j<a.length; j++) {
                if(greater(a[minIndex],a[j])) {
                	//更换最小值所在索引
                    minIndex = j;
                }
            }
            //交换索引i和索引minIndex处的值
            exch(a, i, minIndex);
        }
    }

    // 比较v元素是否大于w元素
    private static boolean greater(Comparable v, Comparable w) {
        return v.compareTo(w) > 0;
    }

    // 交换数组a中，索引i和索引j处的值
    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```

## 2.2 复杂性及稳定性分析
 - **时间复杂度**：O(n^2)；
 - **空间复杂度**：O(1)；
 - **稳定性**：不稳定；

# 3 插入排序
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201226175814969.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70#pic_center)

## 3.1 代码
```java
public class Insertion {
    // 对数组a中的元素进行排序
    public static void sort(Comparable[] a) {
        for(int i=1; i<a.length; i++) {
            // 当前元素为a[i],依次和i前面的元素比较，找到一个小于a[i]的元素
            for(int j=i; j>0; j--) {
                if (greater(a[j-1], a[j])) {
                    //交换元素
                    exch(a, j, j-1);
                }else {
                    break;
                }
            }
        }
    }
    
    // 比较v元素是否大于w元素
    private static boolean greater(Comparable v, Comparable w) {
        return v.compareTo(w) > 0;
    }

    // 交换数组a中，索引i和索引j处的值
    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```
## 3.2 复杂性及稳定性分析
 - **时间复杂度**：O(n^2)；
 - **空间复杂度**：O(1)；
 - **稳定性分析**：稳定；

# 4 希尔排序 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201226175902777.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70#pic_center)

## 4.1 代码
```java
public class Shell {

    public static void sort(Comparable[] a) {
        int N = a.length;
        // 确定增长量h的最大值
        int h = 1;
        while(h < N / 2) {
            h = h * 2 + 1;
        }

        // 当增长量h小于1，排序结束
        while(h >= 1) {
            // 找到待插入的元素
            for(int i=h; i<N; i++) {
                // a[i]就是待插入的元素
                // 把a[i]插入到a[i-h],a[i-2h],a[i-3h]....序列中
                for(int j=i; j>=h; j-=h) {
                    // a[j]就是待插入的元素，依次和a[j-h],a[j-2h],a[j-3h]进行比较，如果a[j]小，那么交换位置
                    if (greater(a[j-h], a[j])) {
                        exch(a, j, j-h);
                    } else {
                        break;
                    }
                }
            }
            h /= 2;
        }
    }
    // 比较v元素是否大于w元素
    private static boolean greater(Comparable v, Comparable w) {
        return v.compareTo(w) > 0;
    }

    // 交换数组a中，索引i和索引j处的值
    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```
## 4.2 复杂性及稳定性分析
 - **时间复杂度**：最好情况O(n)，最坏情况O(n ^ 2)，平均O(n ^ 1.3)；
 - **空间复杂度**：O(1)；
 - **稳定性**：不稳定；
 - 经过测试，希尔排序性能远高于插入排序；

# 5 归并排序 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201226175947280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70#pic_center)

## 5.1 代码
```java
public class Merge {
    //定义一个辅助数组
    private static Comparable[] assist;

    //对数组a中的元素进行排序
    public static void sort(Comparable[] a) {
        assist = new Comparable[a.length];
        int lo = 0;
        int hi = a.length-1;
        sort(a,lo,hi);
    }

    //对数组a从lo到hi的元素进行排序
    private static void sort(Comparable[] a,int lo, int hi) {
        if (lo>=hi) {
            return;
        }

        int mid = (lo + hi) / 2;
        //对lo到mid之间的元素进行排序
        sort(a, lo, mid);
        //对mid+1到hi之间的元素进行排序
        sort(a, mid+1, hi);
        //对lo到mid这组数组和mid+1到hi这组数据进行归并
        merge(a, lo, mid, hi);
    }

    //对数组中，从lo到mid为一组，从mid+1到hi为一组，对这两组数据进行归并
    private static void merge(Comparable[] a, int lo, int mid, int hi) {
        int i = lo;//定义一个指针，指向assist数组中开始填充数据的索引
        int p1 = lo;//定义一个指针，指向第一组数据的第一个元素
        int p2 = mid+1;//定义一个指针，指向第二组数据的第一个元素

        //lo到mid这组数据和mid+1到hi这组数据归并到辅助数组assist对应的索引处
        //比较左边小组和右边小组中的元素大小，如果右边比左边小，把右边数据填充到assist数组中，否则填充左边
        while (p1<=mid && p2<=hi) {
            if (greater(a[p1], a[p2])) {
                assist[i++] = a[p2++];
            } else {
                assist[i++] = a[p1++];
            }
        }

        //上面的循环结束后，如果退出循环的条件是p1<=mid，则证明左边小组中的数据已经归并完毕；如果退出循环的条件是p2<=hi，则证明右边小组的数据已经填充完毕
        //所以需要把未填充完毕的数据继续填充到assist中，下面两个循环，会执行其中一个
        while (p1<=mid) {
            assist[i++] = a[p1++];
        }
        while (p2<=hi) {
            assist[i++] = a[p2++];
        }
        //到目前为止，assist数组中从lo到hi的元素都是有序的，再把数据拷贝到a数组中对应的索引处
        for (int index=lo;index<=hi;index++) {
            a[index] = assist[index];
        }
    }

    // 比较v元素是否大于w元素
    private static boolean greater(Comparable v, Comparable w) {
        return v.compareTo(w) > 0;
    }
}
```

## 5.2 复杂性及稳定性分析
 - **时间复杂度**：O(nlogn)；
 - **空间复杂度**：O(n)；
 - **稳定性**：稳定；
 - 与希尔排序在处理大批量数据时性能差别不大；

# 6 快速排序 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201226180057373.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70#pic_center)

## 6.1 代码
```java
public class Quick {
    public static void sort(Comparable[] a) {
        int lo = 0;
        int hi = a.length - 1;
        quicksort(a, lo, hi);
    }

    private static void quicksort(Comparable[] a, int lo, int hi) {
        if (lo >= hi) return;
        Comparable key = a[lo]; // 把最左边的元素当作基准值
        int left = lo; // 定义一个左指针，初始指向最左边元素
        int right = hi; // 定义一个右指针，初始指向最右边元素
        // 进行切分
        while (left < right){
            // 先从右往左扫描，找到一个比基准值小的元素
            while(left < right && greaterOrequal(a[right],key)){
                right--;
            }

            // 再从左往右扫描，找一个比基准值大的元素
            while (left < right && greaterOrequal(key,a[left])){
                left++;
            }

            //交换left和right索引处的元素
            exch(a, left, right);
        }
        // 交换最后left索引处和基准值所在索引处的值，交换right也可以，因为left和right最终会相等
        exch(a, lo, left);
        // 继续迭代
        quicksort(a, lo, left - 1);
        quicksort(a, left + 1, hi);
    }

    // 比较v元素是否大于等于w元素
    private static boolean greaterOrequal(Comparable v, Comparable w) {
        return v.compareTo(w) >= 0;
    }

    // i索引和j索引处的元素互换
    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```
## 6.2 复杂性及稳定性分析
 - **时间复杂度**：最好情况O(nlogn)，最坏情况O(n^2)，平均O(nlogn)；
 - **空间复杂度**：O(logn)；
 - **稳定性**：不稳定；

# 7 测试

```java
public class Test {
    public static void main(String[] args) throws Exception {
        Integer[] arr = {8,6,1,2,7,40,8,56,21,88};
//        Bubble.sort(arr);
//        Selection.sort(arr);
//        Insertion.sort(arr);
//        Shell.sort(arr);
//        Merge.sort(arr);
        Quick.sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

```java
[1, 2, 6, 7, 8, 8, 21, 40, 56, 88]
```

# 8 排序的稳定性
## 8.1 概念
 - 数组arr有若干元素，其中A等于B，并且A在B前，如果使用某种排序算法，能保证A依然在B前，那么该排序算法就是稳定的。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201021165057867.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70#pic_center)
## 8.2 意义
 1. 如果一组数据只需要一次排序，稳定性没有意义；
 2. 一组数据需要多次排序，稳定性有意义；
 3. 例如对一组商品对象排序，第1次排按**价格**低到高，第二次按**销量**低到高。如果第二次排序稳定，那么相同销量的商品依然保持价格低到高的顺序展现，只有销量不同的对象才需要重新排序。如此既保持了第一次排序的意义，又减少了系统开销；

## 8.3 排序算法的稳定性分析
### 8.3.1 冒泡排序
只有当arr[i]>arr[i+1]的时候，才会交换元素位置，相等的时候不交换。

所以冒泡排序是稳定的。
### 8.3.2 选择排序
选择排序是给每个位置选择当前元素最小的。例如有数据{5(1),8,5(2),2,9},第一遍选择到的最小元素为2，所以5(1)会和2进行位置交换，此时5(1)到了5(2)的后面，破坏了稳定性。

所以选择排序是不稳定的。

### 8.3.3 插入排序
插入排序是从有序序列的末尾开始，也就是想要插入的元素和已经有序的最大者开始比起，如果不比它小则直接插入在其后面，否则一直往前找直到找到它插入的位置。

如果遇到一个和插入元素相等的，那么把要插入的元素放在相等元素的后面，相等元素的前后顺序没有改变。

所以插入排序是稳定的。

### 8.3.4 希尔排序
希尔排序是按照不同步长对元素进行插入排序，虽然一次插入排序是稳定的，不会改变相同元素的相对顺序，但在不同的插入排序过程中，相同的元素可能在各自的插入排序中移动，最后其稳定性就会被打乱。

所以希尔排序是不稳定的。

### 8.3.5 归并排序
归并排序在归并过程中，只有当前面的元素比后面的元素大时才会交换位置，如果元素相等则先加入前面的元素，不会破坏稳定性。

所以归并排序是稳定的。

### 8.3.6 快速排序
快速排序需要一个基准值，在基准值右侧找一个比基准值小的元素，在基准值左侧找一个比基准值大的元素，然后交换这两个元素，会破坏稳定性。

所以快速排序是不稳定的。

