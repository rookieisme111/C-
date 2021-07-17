# **面试常见链表算法及代码**
1. **定义一个链表节点**
```
  struct ListNode{
      int value;
      ListNode* next;
      ListNode(int x):value(x),next(nullptr){}
  }
```

2. **206.反转链表**
* 递归
```
ListNode* reverseList(ListNode* head) {
    if (!head || head->next==nullptr)  return head;

    ListNode* new_head=reverseList(head->next);       
    head->next->next=head;                      //head此时的后继节点是翻转后子链表的尾节点
    head->next=nullptr;                         //将head插入到翻转链表的尾部

    return new_head;

}
```
* 非递归,采用头插法，依次将就旧链表的节点取出，插入新链表的头部，最终实现翻转
```
ListNode* reverseList(ListNode* head) {
    ListNode* prev=nullptr,*tmp;
    while(head){
        tmp=head->next;
        head->next=prev;
        prev=haed;
        head=tmp;

    }
    return prev;
}
```
3. **21.合并两个有序链表**
* 非递归
  借助一个哑节点来记录新链表的开始，再定义一个节点表示新链表的尾节点
 ```
 ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode* dummy = new ListNode(0);
    ListNode* tail = dummy;
    while(l1 && l2){
        if (l1->val<l2->val){
            tail->next=l1;
            l1 = l1->next;
        }
        else{
            tail->next = l2;
            l2=l2->next;
        }
        tail = tail->next;
    }
    tail->next = l1?l1:l2;
    return dummy->next;
}
 ```
 * 递归
 ```
 ListNode* mergeTwoLists(ListNode* l1,ListNode* l2){
    if (!l1)
        return l2;
    if (!l2)
        return l1;
    if (l1->val > l2->val){
        l2->next = mergeTwoLists(l1,l2->next);
        return l2;
    }
    l1->next = mergeTwoLists(l1->next,l2);
    return l1;
}
 ```
 4. **24.成对交换链表节点**
 5. **160.判断两个链表是否相交**
 * 两个指针分别从两个链表的头节点出发，当指针跑到尾节点后，切换至另一个链表的头节点继续遍历，若遍历结束前两个指针没有指向同一个节点，则两个链表不相交，否则指针指向节点就是相交节点
 ```
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
  ListNode* l1 = headA,*l2 = headB;
  while(l1!=l2){
      l1 = l1?l1->next:headB;
      l2 = l2?l2->next:headA;
  }
  return l1;
}
 ```
 * 将一个链表头尾相连，如果此时另一个链表有环，则表明两个链表相交
 7. **判断链表中是否有环**
 * 快慢指针：快慢指针同时从头节点出发，快指针每次前进两步，慢指针每次前进一步，快指针先于慢指针进入环，当慢指针进入环时，快指针在环上的某个位置，之后每前进一次，两个指针间的距离就缩短1，在慢指针走完一圈前，快指针一定可以追上慢指针。
 9. **如果一个链表有环，找到环的入口**
 10. **如果存在环，求出环上节点数量**
 
