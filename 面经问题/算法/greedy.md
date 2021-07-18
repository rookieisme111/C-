# **贪心算法**

> 贪心思想：保证每次操作的结果是局部最优的，从而最终的结果是全局最优的。

1. **455.分配饼干**

```
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        int count=0;        //表示找到的可以满足胃口的孩子的数量
        sort(g.begin(),g.end());
        sort(s.begin(),s.end());
        int len_g=g.size();
        int len_s=s.size();
        int i=0,j=0;            //表示指向饼干矢量的下标指针
        while(i<len_g && j<len_s){
            if (g[i]<=s[j]){
                count++;
                i++;
            }
            j++;
        }
        return count;
    }
};
```

2. **135.分发糖果**

3. **605.种花问题**
* 对于一片长度为l的未种植花坛（花坛最外边可以种植），其最多可以种植（l+1）/2朵花，将现有空白花坛分为两种，一侧没有限制，两侧都种植花的
* 技巧：**设置一个prev表示上一个种植花的位置索引，初始为-1**

4. **区间问题:435无重叠区间**
> 求移除最少数量的区间使其不重叠，转换为最多有多少个不重叠区间
> 首先找出所有区间中，右边界最靠左的区间，此时它能给剩余区间留下更多空间，在剩余与其不重叠的区间中继续寻找不重叠的区间
```
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        sort(intervals.begin(),intervals.end(),[](vector<int> a,vector<int> b ){
            return a[1]<b[1];
        });
        int ans = 1;        //不重叠区间数量
        int pos = intervals[0][1]; //第一个区间的有边界
        for (vector<int> interval:intervals){
            if(interval[0]>=pos){
                pos = interval[1];
                ans++;
            }
        }
        return intervals.size()-ans;
    }
```

5. **区间问题：452用最少的箭引爆气球**
> 多个气球，一个右边界最靠左的气球，若想引爆它弓箭最多为其右边界，此时箭也可以尽可能多的射到其它气球

6. **406.根据身高重建队列**

7. **763.划分字母区间：同一字母只能出现在一个子字符串区间内**

