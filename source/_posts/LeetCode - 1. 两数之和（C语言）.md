---
title: LeetCode - 1. 两数之和（C语言）
categories: LeetCode
date: 2018-07-31 17:24:29
---


给定一个整数数组和一个目标值，找出数组中和为目标值的两个数。
你可以假设每个输入只对应一种答案，且同样的元素不能被重复利用。

示例:
```
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

<!--more-->


### C语言

```
    #include <stdio.h>
    #include <stdlib.h>

    int* twoSum(int* nums, int numsSize, int target) {

      int min = 2147483647;
      int i = 0;
      for (i = 0; i < numsSize; i++) {
          if (nums[i] < min)
              min = nums[i];
      }
      int max = target - min;
      int len = max - min + 1;   //确定hash长度
      int *table = (int*)malloc(len*sizeof(int));
      int *indice = (int*)malloc(2*sizeof(int));
      for (i = 0; i < len; i++) {
          table[i] = -1;         //hash初值
      }
      for (i = 0; i < numsSize; i++) {
          if (nums[i]-min < len) {
              if (table[target-nums[i]-min] != -1) {        //满足相加为target
                  indice[0] = table[target-nums[i] - min];
                  indice[1] = i;
                  return indice;
              }
              table[nums[i]-min] = i;
          }
      }
      free(table);
      return indice;
    }


```
>

```
    int main() {

       int a[4] = {2,7,11,13};
       int tag = 9;
       int *num = twoSum(a,4,tag);
          printf("%d-%d",num[0],num[1]);

       return 0;
    }

```

### Swift

```
class LeetCode001 {
    //第一题
    //给定一个整数数组，找出其中两个数满足相加等于你指定的目标数字。

    func logData() {
        //###1 [2,7,11,15]
        let array:[Int] = [2,7,11,15]
        let target:Int  = 9
        print(LeetCode001().twoSum(array, target))

    }


    func twoSum(_ nums: [Int], _ target: Int) -> [Int] {
        var array1 = [Int]()

        for i in 0  ..< nums.count  {
            for j in i + 1  ..< nums.count  {
                if nums[i]+nums[j] == target {
                    //具体数据
                    array1.append(i)
                    array1.append(j)
                    return array1;
                }
            }
        }
        return array1;
    }

}
```

