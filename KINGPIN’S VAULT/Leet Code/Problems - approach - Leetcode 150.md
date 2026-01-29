# Arrays and  Strings:

> [!danger]+ Important Methods and shortcut functions :
> - memcpy(dest,src,n*sizeof(int)) - for copying one array to another array

> [!danger]+ Use of compare function in qsort:
> What the Comparison Function Returns:
> The comparison function must return one of the following values:
> 
> A Negative Value: If the first element is less than the second element.
> 
> return (*(int*)a - *(int*)b);
> In this case, qsort will place the element pointed to by a before the element pointed to by b.
> 
> Zero: If the two elements are equal.
> 
> return 0;
> This means that the order of these two elements does not matter.
> 
> A Positive Value: If the first element is greater than the second element.
> 
> return (*(int*)b - *(int*)a);
> In this case, qsort will place the element pointed to by a after the element pointed to by b.



88: [Merge Sorted array]:-

Bruteforce:
```
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        if(n!=0){
            for(int i=0;i<n;i++){
                nums1[m++] = nums2[i];
            }
            Arrays.sort(nums1);
        }
    }
}
```

Optimal approach: [https://www.youtube.com/watch?v=TTWKBqG-6IU]
```
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m-1;
        int j = n-1;
        int right = m+n-1;
        while(j>=0){
            if(i>=0 && nums1[i]>nums2[j]){
                nums1[right] = nums1[i];
                i--;
            }else{
                nums1[right] = nums2[j];
                j--;
            }
            right--;
        }
   }
}
```

> [!info]+ Optimal Approach
> ```
> c- programming:
> 
> void merge(int* nums1, int n1, int m, int* nums2, int n2, int n) {
>     int i = m-1;
>     int j = n-1;
>     int right  = n1-1;
>     while(j>=0){
>         if(i>=0 && nums1[i]>nums2[j]) nums1[right--] = nums1[i--];
>         else nums1[right--] = nums2[j--];
>     }
> }
> ```



==i >= 0== is checked becoz :--
nums1 = [3,4,5,0,0,0],  m = 3 
nums2 = [1,2,6],       n = 3
so here after 4 iterations there will be stack overflow error
==j>=0== ===> this is done because only checking that is enough becoz if lesser elements present in the arr1 na then they are already sorted and they are in the correct position placing this elements in nums1 is just enough


27 : [Remove elements]
```
class Solution {
    public int removeElement(int[] nums, int val) {
        int left = 0;
        int right = 0;
        while(right<nums.length){
            if(nums[right]!=val){
                nums[left++] = nums[right];
            }
            right++;
        }
        return left;
    }
}
```

> [!info]+ Optimal Approach
> ```
> int removeElement(int* nums, int n, int val) {
>     int x = 0;
>     for(int i=0;i<n;i++){
>         if(nums[i]!=val) nums[x++] = nums[i];
>     }
>     return x;
> }
> ```


26: [[Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)]

```
class Solution {
    public int removeDuplicates(int[] nums) {
        int left=0,right = 0;
        while(right<nums.length-1){
            if(nums[right]!=nums[right+1]){
                nums[left++] = nums[right];
            }
            right++;
        }
        nums[left++] = nums[right];
        return left;
    }
}
```

> [!info]+ Optimal Approach
> ```
> int removeDuplicates(int* nums, int n) {
>     int left = 0;
>     for(int i=0;i<(n-1);i++){
>         if(nums[i]!=nums[i+1]) nums[left++] = nums[i];
>     }
>     nums[left] = nums[n-1];
>     return (left+1);
> }
> ```

80: [Remove duplicates from sorted array 2]

> [!note]+ Own
> ```
> int removeDuplicates(int* nums, int n) {
>     int count = 0;
>     int x = 0;
>     for(int i=0;i<(n-1);i++){
>         if(nums[i]==nums[i+1]) count++;
>         else{
>             if(count >= 1){
>                 nums[x++] = nums[i];
>             }
>             nums[x++] = nums[i];
>             count = 0;
>         }
>     }
>     if(count>=1) nums[x++] = nums[n-1];
>     nums[x] = nums[n-1];
>     return (x+1);
> }
> ```

169 - majority element:

> [!tip]+ Optimal Approach
> ```
> int majorityElement(int* nums, int n) {
>     int count = 1;
>     int candidate = nums[0];
>     if(n==1) return candidate;
>     for(int i=1;i<n;i++){
>         if(candidate==nums[i]) count++;
>         else count--;
> 
>         if(count == 0){
>             candidate = nums[i];
>             count++;
>         } 
>     }
>     return candidate;
> }
> ```

189: Rotate Array:

> [!failure]+ warning:
> 1st copy the array and then do the operation 

> [!example]+ Optimal Approach
> ```
> #include <string.h>
> void rotate(int* nums, int n, int k) {
>     int res[n];
>     memcpy(res,nums,n*sizeof(int));
>     for(int i=0;i<n;i++){
>         nums[(i+k)%n] = res[i];
>     }   
> }
> ```

121: Best Time to buy and sell stocks

> [!example]+ Optimal Approach:
> ```
> int maxProfit(int* nums, int n) {
>     int buy = nums[0];
>     int max = 0;
>     for(int i=1;i<n;i++){
>         if(nums[i]<buy) buy = nums[i];
>         else max = (nums[i]-buy)>max?(nums[i]-buy):max;
>     }
>     return max;
> }
> ```

122: Best Time to buy and sell stocks

> [!example]+ Optimal:
> ```
> int maxProfit(int* nums, int n) {
>     int profit = 0;
>     for(int i=0;i<(n-1);i++) if(nums[i]<nums[i+1]) profit += nums[i+1]-nums[i];
>     return profit;
> }
> ```


55.jump game

> [!example]+ Optimal
> ```
> bool canJump(int* nums, int n) {
>     int goal = n-1;
>     for(int i=n-2;i>=0;i--){
>         if(nums[i]+i>=goal) goal = i;
>     }
>     return goal==0;
> }
> ```

45:Jump Game II

> [!example]+ Optimal: [Jump Game II](https://leetcode.com/problems/jump-game-ii/?envType=study-plan-v2&envId=top-interview-150)
> ```
> int jump(int* nums, int n) {
>     int near=0,far=0,jump=0;
>     while(far<n-1){
>         int farthest = 0;
>         for(int i=near;i<=far;i++){
>             farthest = farthest<(nums[i]+i)?(nums[i]+i):farthest;
>         }
>         near = far+1;
>         far = farthest;
>         jump++;
>     }
>     return jump;
> }
> ```

274: H-index

> [!example]+ Optimal: [H-index](https://leetcode.com/problems/h-index/?envType=study-plan-v2&envId=top-interview-150)
> ```
> #include <stdlib.h>
> 
> int compare(const void *a, const void *b){
>     return *(int*)a - *(int*)b;   
> }
> 
> int hIndex(int* nums, int n) { 
>     int max = 0;
>     qsort(nums,n,sizeof(int),compare);
>     for(int i=0;i<n;i++){
>         if(nums[i]>=n-i){
>             max = max>(n-i)?max:(n-i);
>         }
>     }
>     return max;
> }
> ```

238: Product of array except self

> [!example]+ OWN with TLE[TIme Limit Exceeded]:
> [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)
> ```
> int* productExceptSelf(int* nums, int n, int* returnSize) {
>     int* res = (int*)malloc(n * sizeof(int));
> 
>     for(int i = 0; i < n; i++){
>         int product = 1;
>         for(int j = 0; j < n; j++){
>             if(j != i)       
>                 product *= nums[j];
>         }
>         res[i] = product;
>     }
>     *returnSize = n;   
>     return res;
> }
> ```

> [!example]+ Optimal:
> [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)
> ```
> int* productExceptSelf(int* nums, int n, int* returnSize) {
>     int* res = (int*)malloc(n*sizeof(int));
>     int zeros = 0;
>     int product = 1;
>     for(int i=0;i<n;i++){
>         res[i] = 0;
>         if(nums[i]==0) zeros++;
>         else product*=nums[i];
>     }
>     if(zeros==1) for(int i=0;i<n;i++) res[i] = nums[i]==0?product:0;
>     else if(zeros==0) for(int i=0;i<n;i++) res[i] = product/nums[i];
> 
>     *returnSize = n;
>     return res;
> }
> ```

LC - 134:

> [!example]+ Optimal: [Gas Station](https://leetcode.com/problems/gas-station/description/?envType=study-plan-v2&envId=top-interview-150)
> ```
> int canCompleteCircuit(int* gas, int gs, int* cost, int cs) {
>     int tot_cost=0,tot_gas=0,start = 0,fuel = 0;
>     for(int i=0;i<gs;i++){
>         tot_cost+=cost[i];
>         tot_gas+=gas[i];
>         
>         // this is for finding the start if this (tot_cost>tot_gas) fails
>         // if this (tot_cost>tot_gas) passes there is no need to check all the gas stations in the round[whole array by coming beckwards also] becoz if total_gas>=total_cost then it is definitely going to be passed
>         fuel += gas[i]-cost[i];
>         if(fuel<0){
>             start =i+1;
>             fuel = 0;
>         }
>     }
>     if(tot_cost>tot_gas) return -1;
>     return start;
> }
> ```

LC - 135:

> [!example]+ Optimal: [Candy](https://leetcode.com/problems/candy/description/?envType=study-plan-v2&envId=top-interview-150)
> ```
> int candy(int* nums, int n) {
>     int res[n];
>     for(int i=0;i<n;i++) res[i] = 1;
>     //left to right 
>     for(int i=1;i<n;i++) if(nums[i]>nums[i-1]) res[i] = res[i-1]+1;
>     //right to left
>     for(int i=n-2;i>=0;i--) if(nums[i]>nums[i+1] && res[i]<=res[i+1]) res[i] = res[i+1]+1;
>     int tot = 0;
>     for(int i=0;i<n;i++) tot+=res[i];
>     return tot;
> }
> ```

LC - 42:

> [!example]+ Optimal: [Trapping The Rain water](https://leetcode.com/problems/trapping-rain-water/?envType=study-plan-v2&envId=top-interview-150)
> ```
> int trap(int* nums, int n) {
>     int left = 0;
>     int right = n-1;
>     int l_max = nums[left];
>     int r_max = nums[right];
>     int c = 0;
>     while(left<right){
>         if(nums[left]<nums[right]){
>             left++;
>             l_max = l_max>nums[left]?l_max:nums[left];
>             c+=l_max-nums[left];
>         }
>         else{
>             right--;
>             r_max = r_max>nums[right]?r_max:nums[right];
>             c+=r_max-nums[right];
>         }
>     }
>     return c;
> }
> ```

