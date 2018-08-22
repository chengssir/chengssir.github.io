---
title: LeetCode - 4. 两个排序数组的中位数（C语言）
categories: LeetCode
date: 2018-08-02 17:24:29
---


给定两个大小为 m 和 n 的有序数组 nums1 和 nums2 。

请找出这两个有序数组的中位数。要求算法的时间复杂度为 O(log (m+n)) 。

你可以假设 nums1 和 nums2 均不为空。

<!--more-->

```
示例 1:
nums1 = [1, 3]
nums2 = [2]
中位数是 2.0  

示例 2:
nums1 = [1, 2]
nums2 = [3, 4]
中位数是 (2 + 3)/2 = 2.5
```

### C语言

```
double findMedianSortedArrays(int* nums1, int nums1Size, int* nums2, int nums2Size) {

    int numSize = nums1Size+nums2Size;
    int s[numSize];
    int i,l=0,r=0;

    for (i = 0; i <= (numSize/2);) {

        if (l == nums1Size){
            while (i <= (numSize/2)){
                s[i++] = nums2[r++];
            }
        } else if (r == nums2Size){
            while (i <= (numSize/2)){
                s[i++] = nums1[l++];
            }
        } else{
            if(nums1[l] < nums2[r]){
                s[i++] = nums1[l++];
            } else{
                s[i++] = nums2[r++];
            }
        }
//        for (int j = 0; j < numSize; ++j) {
//            printf("%d-",s[j]);
//        }
//        printf("\n");

    }
//    for (int j = 0; j < numSize; ++j) {
//        printf("%d-",s[j]);
//    }
//
//    printf("\n");

    if(numSize%2==1){
        return s[i-1] ;
    }else{
        return ((s[i-2]+s[i-1]) / 2.0);
    }

}

```


```
#include <stdio.h>
#include "LeetCode4.cpp"

int main() {
    double findMedianSortedArrays(int* nums1, int nums1Size, int* nums2, int nums2Size);

    int a[4] = {1,2};
    int b[3] = {3,4};
    double num = findMedianSortedArrays(a,2,b,2);
    printf("%.1f",num);
    return 0;
}
```

### Swift

```
class LeetCode004 {
    //第四题
    func findMedianSortedArrays(_ nums1: [Int], _ nums2: [Int]) -> Double {
        var arr:[Int] = nums1 + nums2;
        arr.sort{$0<$1}

        if arr.count == 1 {
            return Double(arr[0])
        }

        let count = arr.count / 2
        if arr.count % 2 == 1 {
            return Double(arr[count])
        }else{
            return (Double(arr[count-1]) + Double(arr[count])) / 2
        }
    }
}

```

