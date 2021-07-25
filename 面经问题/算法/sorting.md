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
5. **归并排序**

6. **堆排序**

7. **希尔排序**

8. **基数排序**

9. **桶排序**
