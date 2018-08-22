---
title: LeetCode - 2. 两数相加（C语言）
categories: LeetCode
date: 2018-08-01 10:24:29
---



给定两个非空链表来表示两个非负整数。位数按照逆序方式存储，它们的每个节点只存储单个数字。将两数相加返回一个新的链表。

你可以假设除了数字 0 之外，这两个数字都不会以零开头。

示例：

输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807

<!--more-->

### C语言
```
#include <stdlib.h>

typedef struct Node{
    int  value;
    struct Node *next;
}SNode;

SNode* addTwoNumbers(SNode *head ,SNode *head_1){

    SNode *p = (SNode*)malloc(sizeof(SNode));
    SNode *p1 = p;
    int add;
    while(head != NULL || head_1 != NULL){

        SNode *p2 = (SNode*)malloc(sizeof(SNode));
        int a = 0;
        int b = 0;
        
        if(head!=NULL)
            a = head->value;
        if(head_1!=NULL)
            b = head_1->value;

        int num =  a+b+add;
        p2->value =  num % 10;
        add = num / 10;

        p2->next = NULL;

        p1->next = p2;
        p1 = p2;

        if(head!=NULL)
            head = head->next;
        if(head_1!=NULL)
            head_1 = head_1->next;


    }

    if(add !=0){
        SNode *p2 = (SNode*)malloc(sizeof(SNode));
        p2->value =  add;
        p2->next = NULL;
        p1->next = p2;
    }

    return p->next;

}

```

```
int main() {

    SNode *head = (SNode*)malloc(sizeof(SNode));

    int a[] = {5};
    int b[] = {5};

    //引用指针 做一个变量
    SNode *p2 = NULL;

    for (int i = 0; i < 1; ++i) {

        if(i == 0){
            head->value = a[i];
            head->next = NULL;
            p2 = head;
        } else{
            SNode *p = (SNode*)malloc(sizeof(SNode));
            p->value = a[i];
            p->next = NULL;

            //上一个的next 指向这一个
            p2->next = p;
            //把这一个p当上一个节点
            p2=p;
        }
    }


    SNode *head_1 = (SNode*)malloc(sizeof(SNode));


    //引用指针 做一个变量
    SNode *p3 = NULL;

    for (int i = 0; i < 1; ++i) {

        if(i == 0){
            head_1->value = b[i];
            head_1->next = NULL;
            p3 = head_1;
        } else{
            SNode *p = (SNode*)malloc(sizeof(SNode));
            p->value = b[i];
            p->next = NULL;

            //上一个的next 指向这一个
            p3->next = p;
            //把这一个p当上一个节点
            p3=p;
        }

    }


    SNode *p4 = addTwoNumbers(head,head_1);


    while(p4)
    {
        printf("%d ",p4->value);
        p4=p4->next;
    }

    printf("\n");

    return 0;
}
```
### Swift
```
class ListNode {
    var val: Int
    var next: ListNode?

    init(_ val: Int) {
        self.val = val
        self.next = nil
    }
}

class LeetCode002 {
    
    func addTwoNumbers(_ l1: ListNode?, _ l2: ListNode?) -> ListNode? {

        var l1 = l1
        var l2 = l2
        var sum = 0
        let result = ListNode(0)
        var temp = result
        var remain = 0

        while l1 != nil || l2 != nil || remain != 0 {
            sum = remain
            if l1 != nil {
                sum += l1!.val
                l1 = l1?.next
            }
            if l2 != nil {
                sum += l2!.val
                l2 = l2?.next
            }
            remain = sum / 10
            sum = sum % 10
            let node = ListNode(sum)
            temp.next = node
            temp = temp.next!
        }
        return result.next
    }
}
```

