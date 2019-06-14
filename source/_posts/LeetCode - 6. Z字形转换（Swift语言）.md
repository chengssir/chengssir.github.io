---
title: LeetCode - 6. Z字形转换（Swift语言）
categories: LeetCode
date: 2019-06-11 19:30:29
---


将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 "LEETCODEISHIRING" 行数为 3 时，排列如下：

```
L   C   I   R
E T O E S I I G
E   D   H   N
```
之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如："LCIRETOESIIGEDHN"。

请你实现这个将字符串进行指定行数变换的函数：
> string convert(string s, int numRows);

```
示例 1:：
输入: s = "LEETCODEISHIRING", numRows = 3
输出: "LCIRETOESIIGEDHN"

示例 2：
输入: s = "LEETCODEISHIRING", numRows = 4
输出: "LDREOEIIECIHNTSG"
解释:
L     D     R
E   O E   I I
E C   I H   N
T     S     G

```

<!--more-->

#### 算法说明
比如有一个字符串 “0123456789ABCDECFHIJ”

转换
2行：

```
0 2 4 6 8    
1 3 5 7 9    
```

3行：

```
0     4     8     C  
1  3  5  7  9  B  D  F 
2     6     A     E         
```

5行

```
0     8     G 
1   7 9   F H
2  6  A  E  I
3 5   B D   J
4     C     K

```

转换数字看:（例1）

```
0     8         16 
1   7 9      15 17
2  6  10   14   18
3 5   11 13     19
4     12        20
```

我们发现第一行和最后一行没两个元素都有一种关系（和行数有一定关系）:2 * rows - 2

5行

| 0 | 2*5-2=8 |
| --- | --- |
| 4 | 4+(2*5-2)=12 |


3行

| 0 | 2*3-2 = 4 |
| --- | --- |
| 2 | 2+(2*3-2) = 6 |

然后我们看下中间的部分的规律(看例1) 1 、 2 、 3 行
其中有部分是符合上述规律（2*rows-2=8），
其两数中间还有一个，
看其中间规律  7 = 8 - 1， 6 = 8 - 2， 5 = 8 - 3，
有没有发现减去的1、2、3正好就是所在行数
所以中间数字的公式 = （2 * rows-2）- i(第i行)




### Swift
```
class leetCode {
    
    func convert(_ s: String, _ numRows: Int) -> String {
        let len: Int = s.count
        guard (len > numRows) && (numRows > 1) else {
            return s
        }
        //字符串转字符数组
        var characters = Array(s)
        // 第一个公式
        let funNum:Int = 2 * numRows - 2
        
        var resultStr: String = ""
        for i in 0..<numRows {
            var j:Int = 0
            while (j + i) < len {
                let aaa = characters[j + i]
                resultStr.append(aaa)
                print(aaa)
                //第二个公式
                let coordi:Int = j + funNum - i
                if i != 0 && i != numRows - 1 && coordi < len{
                    let bbb = characters[coordi]
                    resultStr.append(bbb)
                }
                j += funNum
            }
        }
        print(resultStr)
        return resultStr
    }
}

```

