### 21、合并两个有序链表

```c
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2){
    if (l1 == NULL)
        return l2;
    else if (l2 == NULL)
        return l1;
    struct ListNode *p;
    if (l1->val <= l2->val) {
        p = l1;
        p->next = mergeTwoLists(l1->next, l2);
        return p;
    }
    else {
        p = l2;
        p->next = mergeTwoLists(l1, l2->next);
        return p;
    }
}
```

### 206、反转链表

```c
struct ListNode* reverseList(struct ListNode* head){
    if (head == NULL || head->next == NULL)
        return head;
    struct ListNode *p = head;
    struct ListNode *q = head->next;
    struct ListNode *r;
    p->next = NULL;
    while (q != NULL) {
        r = q->next;
        q->next = p;
        p = q;
        q = r;
    }
    return p;
}
```

