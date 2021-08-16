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
 * 两个指针分别从两个链表的头节点出发，当指针跑到尾节点后，切换至另一个链），则两个链表不相交，否则指针指向节点就是相交节点
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
 * 快慢指针(Floyed判圈法)：快慢指针同时从头节点出发，快指针每次前进两步，慢指针每次前进一步，快指针先于慢指针进入环，当慢指针进入环时，快指针在环上的某个位置，之后每前进一次，两个指针间的距离就缩短1，在慢指针走完一圈前，快指针一定可以追上慢指针。
 ```
*使用do-while,两个指针可以从head出发
bool hasCycle(ListNode *head) {
  ListNode* fast = head, *slow = head;
  do{
      if (!fast || !fast->next)
          return false;
      slow = slow->next;
      fast = fast->next->next;
  }while(slow!=fast);
  return true;
}
 ```
 * 使用while,必须从一个虚拟节点出发，因为先做判断，再移动的。
 9. **如果一个链表有环，找到环的入口**
 * 在判圈法基础上，当快慢指针在一个节点相遇时，此时链表头节点到入口节点的距离等于相遇点到入口节点的距离
 ```
 ListNode* hasCycle(ListNode *head) {
  ListNode* fast = head, *slow = head;
  do{
      if (!fast || !fast->next)
          return NUll;
      slow = slow->next;
      fast = fast->next->next;
  }while(slow!=fast);
  ListNode* p1 = head;
  ListNode* p2 = slow;
  while(p1!=p2){
    p1 = p1->next;
    p2 = p2->next;
  }
  return p1;
}

 ```
 10. **如果存在环，求出环上节点数量**
 * 从入口节点出发，再次回答入口节点经过的步数
 * 从相遇点出发，再次回到相遇的点的距离

 11. **环形链表的长度**
 * 头节点到入口节点的长度+环的长度

 12. **判断一个链表是回文的（O（1）的空间复杂度）**
 * 首先找到链表的中点（快慢指针），然后将后半段的链表逆序，对两个链表进行比较，如果对应位置相等，则为回文链表
 ```
class Solution {
public:
  bool isPalindrome(ListNode* head) {
      if (head == NULL && head->next == NULL)
          return true;

      ListNode* fast = head,*slow = head;
      while (fast!=NULL && fast->next!=NULL){     //这种判断条件，奇数个节点时slow停在中点，偶数个节点时slow停在后半链表的头节点
          slow = slow->next;                      //fast->next && fast->next->next 这种判断条件，奇数个节点时slow停在中点，偶数个节点时停在前半链表的尾节点
          fast = fast->next->next;
      }

      ListNode* new_head = reverseList(slow),*tmp = new_head;

      while (head && new_head ){
          if (head->val!=new_head->val)
              return false;
          head = head->next;
          new_head = new_head->next;
      }
      reverseList(tmp);
      return true;
  }

  ListNode* reverseList(ListNode* head){
      if ( head==NULL && head->next==NULL){
          return head;
      }
      ListNode* prev = NULL, *tmp;
      while(head){
          tmp = head->next;
          head->next = prev;
          prev = head;
          head = tmp;
      }
      return prev;
  }
};
 ```
13. **83.删除升序链表中重复的节点**
```
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head || head->next == NULL)
            return head;
        ListNode* fast = head->next,*slow = head;
        while (fast){
            if (fast->val == slow->val){
                slow->next = fast->next;
                delete(fast);
                fast = slow->next;
            }
            else{
                slow = fast;
                fast = fast->next;
            }

        }
        return head;
    }
```

14. **将一个链表进行重排，原奇数节点排在所有偶数节点的前面**

15. **删除链表中倒数第k个节点**
* 快慢指针+哑节点
```
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        ListNode* prev = dummy,*first,*second;
        first = second = head;
        while(n-->0){
            first = first->next;
        }
        while(first){
            first = first->next;
            prev = second;
            second = second->next;
        }

        prev->next = second->next;
        delete second;
        return dummy->next;
    }
```

16. **排序链表，O（nlogn）的时间复杂度**
* 归并排序+合并两个有序链表+找链表的中点

17. **将链表k个为一组翻转链表**
```
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null || head.next == null){
            return head;
        }
        //定义一个假的节点。
        ListNode dummy=new ListNode(0);
        //假节点的next指向head。
        // dummy->1->2->3->4->5
        dummy.next=head;
        //初始化pre和end都指向dummy。pre指每次要翻转的链表的头结点的上一个节点。end指每次要翻转的链表的尾节点
        ListNode pre=dummy;
        ListNode end=dummy;

        while(end.next!=null){
            //循环k次，找到需要翻转的链表的结尾,这里每次循环要判断end是否等于空,因为如果为空，end.next会报空指针异常。
            //dummy->1->2->3->4->5 若k为2，循环2次，end指向2
            for(int i=0;i<k&&end != null;i++){
                end=end.next;
            }
            //如果end==null，即需要翻转的链表的节点数小于k，不执行翻转。
            if(end==null){
                break;
            }
            //先记录下end.next,方便后面链接链表
            ListNode next=end.next;
            //然后断开链表
            end.next=null;
            //记录下要翻转链表的头节点
            ListNode start=pre.next;
            //翻转链表,pre.next指向翻转后的链表。1->2 变成2->1。 dummy->2->1
            pre.next=reverse(start);
            //翻转后头节点变到最后。通过.next把断开的链表重新链接。
            start.next=next;
            //将pre换成下次要翻转的链表的头结点的上一个节点。即start
            pre=start;
            //翻转结束，将end置为下次要翻转的链表的头结点的上一个节点。即start
            end=start;
        }
        return dummy.next;


    }
    //链表翻转
    // 例子：   head： 1->2->3->4
    public ListNode reverse(ListNode head) {
         //单链表为空或只有一个节点，直接返回原单链表
        if (head == null || head.next == null){
            return head;
        }
        //前一个节点指针
        ListNode preNode = null;
        //当前节点指针
        ListNode curNode = head;
        //下一个节点指针
        ListNode nextNode = null;
        while (curNode != null){
            nextNode = curNode.next;//nextNode 指向下一个节点,保存当前节点后面的链表。
            curNode.next=preNode;//将当前节点next域指向前一个节点   null<-1<-2<-3<-4
            preNode = curNode;//preNode 指针向后移动。preNode指向当前节点。
            curNode = nextNode;//curNode指针向后移动。下一个节点变成当前节点
        }
        return preNode;
    }
}
```
