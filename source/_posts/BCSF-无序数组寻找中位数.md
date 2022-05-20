---
title: 无序数组寻找中位数
date: 2022-03-26 11:02:15
tags: [数组]
categories: 编程算法
widgets: null
password: 328
abstract: 这是一篇加密博文，请输入密码后查看
message: 这里需要密码才能访问。
wrong_pass_message: 抱歉, 这个密码看着不太对, 请再试试.

---

中位数为一个排序数组的中间元素。假设对于一个排序数组A，数组长度为n，如果n为奇数，即中位数为A[(n-1)/2]；如果n为偶数，中位数则是(A[n/2]+A[n/2-1])/2。那么对于一个无序数组，应该如何寻找它的中位数？

## 1. 排序

一种很容易想到的方法就是对无序数组排序，然后可以直接得到该数组的中位数，时间复杂度为O(nlogn)。



```php
# To obtain the median of an unsorted array;
#  by quick sort;
def quickSort(array, start, end): 
    if  len(array) < 2: 
        return 
    if start >= end: 
        return 
    left,right,pivot = start,end,array[start] 
    while left < right: 
        while left < right and array[right] >= pivot: 
            right -= 1
        while left < right and array[left] <= pivot: 
            left += 1 
            array[left],array[right] = array[right],array[left] 
    array[start] = array[left]
    array[left] = pivot
    quickSort(array,start,left-1) 
    quickSort(array,left+1,end)

def findMedian(array) -> float: 
    if not array: 
        return None 
    length = len(array) 
    quickSort(array,0,length-1) 
    print(array) 
    if len(array)%2:
        return array[(length-1)//2]
    else:
        return (array[length//2-1]+array[length//2])/2 

Output:
findMedian([1,3,4,5,6,3]) #3.5
 
```

## 2. 利用快排思想寻找第K大的数（时间复杂度为O(n)）

我们可以使用快排思想快速找中位数，即先挑选一个数作为标准，以该元素为支点，将数组划分为两部分。这个问题可以抽象化为寻找第K大的数，快排每排完一轮之后左侧都是比他小的元素，右侧都是比他大的元素，那么支点的index（假设为N-1）即为第N大的数。当N == K，我们就找到了第K大的数；当N > K时， 第K大的数在[0,N-1]范围内；当N < K时，第K大的数在[N+1,n-1] (n为数组长度)范围内，利用递归即可找到第K大的数。

具体到寻找中位数，可分为两种情况：如果数组长度为奇数，即为寻找第(n+1//2)大的数；如果为偶数，则为分别寻找第(n//2+1)和第(n//2)大的数，然后求其平均值。具体程序如下：



```php
def selectKthNum(array, k, start, end):
    if not array or k < 0 or start >= end:
        return
    left,right,pivot = start,end,array[start]
    while left < right:
        while array[right] >= pivot and left < right:
            right -= 1
        while array[left] <= pivot and left < right:
            left += 1
        if left < right:
            array[left],array[right] = array[right],array[left]
    array[start] = array[left]
    array[left] = pivot
    
    if left == k:
        return
    elif left < k:
        selectKthNum(array, k, left+1, end)
    else:
        selectKthNum(array, k, start, left-1)
        
def findMedian(array):
    if len(array) <= 0:
        return None
    length = len(array)
    if length%2:
        selectKthNum(array, (length-1)//2, 0, length-1)
        return array[(length-1)//2]
    else:
        selectKthNum(array,length//2-1, 0, length-1)
        selectKthNum(array,length//2,length//2-1, length-1)
        return (array[length//2]+array[length//2-1])/2

Output:
array = [1,5,3,4,6]
findMedian(array) # 4
array = [1,5,3,4,6,9]
findMedian(array) # 4.5
```

## 3. 最小堆（时间复杂度为O(nlog(k))）

我们可以构造一个最小堆，通过维护最小堆，即可得到无序数组的中位数。同样地，这个问题可以延伸至求数组的第K小的数。

1. 取n//2+1个数建堆，将数组中剩余项与堆顶项进行比较，如果比堆顶项小，则直接丢弃；如果比堆顶项大，则入堆，然后重新调整堆的结构，更新堆顶元素，并推出堆中最小的数。
2. 如果数组长度为奇数，堆顶项即为中位数；如果数组长度为偶数，则中位数为堆顶项以及推出第一个堆顶项之后的数之平均。



```php
import heapq
def findMedian(array):
    if not array:
        return 
    if len(array) <= 1:
        return array[0]
    length = len(array)
    minHeap = array[0:length//2+1]
    heapq.heapify(minHeap)
    i = length//2 + 1
    while i < length:
        heapq.heappushpop(minHeap,array[i])
        i += 1
    if length%2:
        return minHeap[0]
    else:
        return (heapq.heappop(minHeap)+minHeap[0])/2
    
array = [2,1,3]
findMedian(array) # 2
array = [1,5,3,4,6,9]
findMedian(array) # 4.5 
```

