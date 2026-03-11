
|Algorithm|Best|Average|Worst|Space|Stable|
|---|---|---|---|---|---|
|Bubble|O(n)|O(n²)|O(n²)|O(1)|Yes|
|Selection|O(n²)|O(n²)|O(n²)|O(1)|No|
|Insertion|O(n)|O(n²)|O(n²)|O(1)|Yes|
|Merge|O(n log n)|O(n log n)|O(n log n)|O(n)|Yes|
|Quick|O(n log n)|O(n log n)|O(n²)|O(log n)|No|
|Heap|O(n log n)|O(n log n)|O(n log n)|O(1)|No|
|Counting|O(n+k)|O(n+k)|O(n+k)|O(k)|Yes|
|Radix|O(d(n+k))|O(d(n+k))|O(d(n+k))|O(n+k)|Yes|
|Bucket|O(n+k)|O(n+k)|O(n²)|O(n+k)|Depends|


Time complexity:
Time complexity != Time Taken

Rate at which the time taken for runnning a code increases with the input size is called time complexity

3 rules for time complexity --->  
- calculate only  the worst case complexity
- avoid constants [very small and it will not affect the complexity when the N [no of steps in an iteration is very large]]
- avoid lower values [very small and it will not affect the complexity when the N [no of steps in an iteration is very large]]
