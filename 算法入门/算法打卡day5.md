### 876、链表的中间节点

遍历一遍求出链表的长度，然后遍历长度的一半。

```c
struct ListNode* middleNode(struct ListNode* head){
    struct ListNode *p = head;
    int count = 0;
    while (p != NULL) {
        p = p->next;
        count++;
    }
    p = head;
    count = count / 2;
    while (count > 0) {
        p = p->next;
        count--;
    }
    return p;
}
```

双指针，一个一次走一格，一个一次走两格。

```c
struct ListNode* middleNode(struct ListNode* head){
    struct ListNode *p = head, *q = head;
    while (q != NULL && q->next != NULL) {
        p = p->next;
        q = q->next->next;
    }
    return p;
}
```

### 19、删除链表的倒数第N个节点

一个指针比另一个多走N个，然后再一起走。详情看第19篇题解。
