---
title: LeetCode - 5. 最长回文子串（C语言）
categories: LeetCode
date: 2018-08-11 19:30:29
---


给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为1000。

```
示例 1：
输入: "babad"
输出: "bab"
注意: "aba"也是一个有效答案。

示例 2：
输入: "cbbd"
输出: "bb"
```

<!--more-->

### C语言
```
#include <string.h>
#include <stdlib.h>

char *preProcess(char *s) {
    int len = strlen(s);;
    if (len == 0) return "^$";
    char *str = (char*)malloc(2*len+2);
    memset(str, 0, 2*len+2);
    for (int i = 0; i <= len * 2 ; i += 2)
        str[i] = '#';
    for (int i = 0; i < len; i++)
        str[2*i+1] = s[i];

    return str;
}

char* longestPalindrome(char* s) {

    char *str = preProcess(s);
    int len = strlen(s);;
    int p[10000],i = 0, mx = 0, id = 0, imax = 0;

    for(i = 0; i <= 2*len; i++) {
        if(mx > i)
            p[i] = mx-i+1<p[2*id-i]? mx-i+1:p[2*id-i];
        else
            p[i] = 1;


        for(; i-(p[i]-1) > 0 && i+(p[i]-1) <= 2*len && str[i-p[i]] == str[i+p[i]]; p[i]++);

        if(i+(p[i]-1) > mx) {
            id = i;
            mx = i+(p[i]-1);
        }
        if(p[i] > p[imax])
            imax = i;

    }
    char *sub = (char*)malloc(p[imax]);
    memset(sub, 0, p[imax]);
    //memcpy(char1,char2,length) 从char2 copy length长度的字符到char1
    memcpy(sub, s+(imax-p[imax]+1)/2, p[imax]-1);

    return sub;
}
```

```
#include <stdio.h>
#include "LeetCode5.cpp"

int main() {
    char greeting[] = "abcbaa";
    char  *str = longestPalindrome(greeting);
    printf("%s\n",str);

    return 0;
}
```

### Swift
```
class LeetCode005 {


    func logData(){

        let string : String = "abcbaw"
        let results : String = LeetCode005().longestPalindrome(string)
        print(results)
    }

    func longestPalindrome(_ s: String) -> String {

        let chars = [Character](s.characters)
        let length = chars.count
        guard length > 0 else {
            return ""
        }
        //创建一个，元素值重复，个数固定的数组
        var isPalidromeMatrix = Array(repeating: Array(repeating: false, count : length), count : length)
        /*
         [
         [false,false,false,false],
         [false,false,false,false],
         [false,false,false,false],
         [false,false,false,false],
         ];
         [];
         */

        var maxLength = 0
        var maxStartIndex = 0

        for palidromeLength in 1 ... length {
            for startIndex in 0 ... length - palidromeLength {
                let endIndex = startIndex + palidromeLength - 1
                var isPalidrome = false

                if palidromeLength == 1 {
                    isPalidrome = true
                } else if palidromeLength == 2 {
                    isPalidrome = chars[startIndex] == chars[endIndex]
                } else {
                    isPalidrome = chars[startIndex] == chars[endIndex] && isPalidromeMatrix[startIndex + 1][endIndex - 1]
                }

                if isPalidrome {
                    isPalidromeMatrix[startIndex][endIndex] = true

                    if palidromeLength > maxLength {
                        maxStartIndex = startIndex
                        maxLength = palidromeLength
                    }
                }
            }
        }

        return String(chars[maxStartIndex...maxStartIndex + maxLength - 1])
    }

}
```

