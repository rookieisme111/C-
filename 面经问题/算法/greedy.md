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

4. **区间问题**
