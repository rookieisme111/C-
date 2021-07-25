# **九大排序算法**

1. **交换排序（冒泡排序）**

2. **插入排序**

3. **选择排序**

4. **快速排序（分治思想）**
* 代码
```
class Solution {
public:
    vector<int> sortArray(vector<int>& nums) {
        if (nums.size()==0)     
            return nums;
        quickSort(nums,0,nums.size()-1);
        return nums;
    }

    void quickSort(vector<int>& nums,int start,int end){
        if (start>=end)
            return;
        int mid = partition(nums,start,end);
        quickSort(nums,start,mid-1);
        quickSort(nums,mid+1,end);
    }

    int partition(vector<int>& nums,int start,int end){
        int randomIndex = rand()%(end-start+1)+start;       //随机选择区间内的一个数作为基数，可以防止数组有序情况下时间复杂度退化到O(n*n)
        swap(nums[start],nums[randomIndex]);
        int pivot = nums[start];
        int left = start+1, right = end;
        while(left<right){
            while(left<right && nums[left]<pivot)   left++;
            while(left<right && nums[right]>pivot)  right--;
            if (left<right){
                swap(nums[left],nums[right]);
                left ++;
                right--;
            }
        }
        if (left==right && nums[right]>pivot)   
            right--;
        swap(nums[start],nums[right]);
        return right;
    }

};
```
5. **归并排序(分治思想)**
* 数组中的归并排序
```
//采用了原地数组合并的方法
class Solution {
public:
    vector<int> sortArray(vector<int>& nums) {
        mergeSort(nums,0,nums.size()-1);
        return nums;
    }

    void mergeSort(vector<int>& nums,int start,int end){
        if (start==end)
            return;
        int mid = (start+end)/2;
        mergeSort(nums,start,mid);
        mergeSort(nums,mid+1,end);
        merge(nums,start,mid,end);
    }

    void merge(vector<int>& nums,int start,int mid,int end){
        int start1 = start,end1 = mid;
        int start2 = mid+1,end2 = end;
        while (start1 <= end1 && start2 <= end2){
            if (nums[start2]>=nums[start1])
                start1++;
            else{
                int temp = nums[start2];
                for (int i=start2;i>start1;i--)
                    nums[i] = nums[i-1];
                nums[start1] = temp;
                start1 ++;
                start2 ++;
                end1 ++;               
            }
        }
    }
};
```
```
//借用了一个额外的数组来合并两个数组
class Solution {
public:
    vector<int> sortArray(vector<int>& nums) {
        vector<int> result(nums.size(),0);
        mergeSort(nums,0,nums.size()-1,result);
        return nums;
    }

    void mergeSort(vector<int>& nums,int start,int end,vector<int>& result){
        if (start==end)
            return ;
        int mid = (start+end)/2;
        mergeSort(nums,start,mid,result);
        mergeSort(nums,mid+1,end,result);
        merge(nums,start,mid,end,result);
    }

    void merge(vector<int>& nums,int start,int mid,int end,vector<int>& result){
        int start1 = start,end1 = mid;
        int start2 = mid+1,end2 = end;
        int resultIndex = start1;
        while(start1<=end1 && start2<=end2){
            if (nums[start1]<nums[start2])
                result[resultIndex++] = nums[start1++];
            else
                result[resultIndex++] = nums[start2++];
        }
        while(start1<=end1)
            result[resultIndex++] = nums[start1++];
        while(start2<=end2)
            result[resultIndex++] = nums[start2++];
        
        for(int i=start;i<=end;i++)
            nums[i] = result[i];
    }
};
```
* 链表中的归并排序
```
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        if (!head || !head->next)   return head;
        ListNode * mid=endOfhalf(head);
        ListNode* startOfSecond=mid->next;

        mid->next=nullptr;

        ListNode* p1=sortList(head);
        ListNode* p2=sortList(startOfSecond);
        return mergeTwoLists(p1,p2);
    }

    ListNode* endOfhalf(ListNode* head){
        if (head==nullptr)  return nullptr;

        ListNode* fast=head,*slow=head;
        while (fast->next!=nullptr && fast->next->next!=nullptr){
            fast=fast->next->next;
            slow=slow->next;
        }
        return slow;
    }
    
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* dummy=new ListNode(0,nullptr);
        ListNode* p=dummy;
        while(l1 && l2){
            if (l1->val<l2->val){
                p->next=l1;
                p=l1;
                l1=l1->next;
            }
            else{
                p->next=l2;
                p=l2;
                l2=l2->next;
            }
        }
        if(l1!=nullptr) p->next=l1;
        if(l2!=nullptr) p->next=l2;
        return dummy->next;
    }
};
```
6. **堆排序**

7. **希尔排序**

8. **基数排序**

9. **桶排序**
