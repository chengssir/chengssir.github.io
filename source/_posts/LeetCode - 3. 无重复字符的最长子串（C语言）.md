---
title: LeetCode - 3. 无重复字符的最长子串（C语言）
categories: LeetCode
date: 2018-08-01 17:24:29
---


给定一个字符串，找出不含有重复字符的最长子串的长度。

示例：

给定 "abcabcbb" ，没有重复字符的最长子串是 "abc" ，那么长度就是3。

给定 "bbbbb" ，最长的子串就是 "b" ，长度是1。

给定 "pwwkew" ，最长子串是 "wke" ，长度是3。请注意答案必须是一个子串，"pwke" 是 子序列  而不是子串。

<!--more-->

* 解题思路：   依次读取字符串的每一个字符，如果第一次出现则当前子串长度+1，否则：首先判断当前长度是否大于最大长度，是则替换最大长度。然后查找重复的字符是在原字符串哪里出现的。

### C语言

```
#include <string.h>

int lengthOfLongestSubstring(char* s) {

    int maxlen = 0,currlen = 0;
    int table[128], i, j, start = 0;
    memset(table, 0, sizeof(table));
    for (i = 0; s[i] != '\0'; ++i){
        int num =  ++table[s[i]];
        if( num == 2 ){
            if (currlen > maxlen){
                maxlen = currlen;
            }
            for(j = start; j < i; ++j){
                if (s[j] == s[i]){
                    table[s[j]] = 1;
                    start = j+1;
                    break;
                }else{
                    --currlen;
                    table[s[j]] = 0;
                }
            }
        }else{
            ++currlen;
        }
    }
    if (currlen > maxlen){
        maxlen = currlen;
    }

    return maxlen;
}
```
 
        
```
int main() {

//    char s[] = "abcdefghiadsdhajdasdsa";
//    char s[] = "abcabcabcda";
    char s[] = "aaaaaaaa";

    int maxlen = lengthOfLongestSubstring(s);

    printf("%d",maxlen);

    return 0;
}      
```


### Swift

```
class LeetCode003 {


    func logData(){

        let string : String = "aabcba"
        let results : Int = LeetCode003().lengthOfLongestSubstring(string)
        print(results)
    }

    //第三题
    func lengthOfLongestSubstring(_ s: String) -> Int {

        //key:Character
        //value:Int
        var letterAppearedDict = [Character:Int]()
        //最大长度
        var maxLength = 0
        //当前长度
        var currentLength = 0
        //Character类型的数组
        var chars = [Character](s.characters)

        for index in 0 ..< chars.count {
            //取值
            let char = chars[index]

            if letterAppearedDict[char] == nil {
                //不存在则添加value 第几位
                letterAppearedDict[char] = index
                //当前长度++
                currentLength += 1
            } else {
                //已经存在，存在第几位？
                let previousIndex = letterAppearedDict[char]!
                //清空重复位置
                for clearIndex in index - currentLength ..< previousIndex {
                    letterAppearedDict[chars[clearIndex]] = nil
                }
                //添加value 第几位
                letterAppearedDict[char] = index
                //当前长度当前位置减去重复下标
                currentLength = index - previousIndex
            }
            //记录最大
            maxLength = max(maxLength, currentLength)
        }


        return maxLength
    }
}
```


