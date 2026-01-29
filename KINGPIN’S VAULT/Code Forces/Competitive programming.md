A. Halloumi Boxes

time limit per test
1 second
memory limit per test
256 megabytes

Theofanis is busy after his [last contest](https://codeforces.com/contest/1594/problem/A), as now, he has to deliver many halloumis all over the world. He stored them inside n boxes and each of which has some number ai written on it.

He wants to sort them in non-decreasing order based on their number, however, his machine works in a strange way. It can only reverse any subarray† of boxes with length at most k.

Find if it's possible to sort the boxes using any number of reverses.

† Reversing a subarray means choosing two indices i and j (where 1≤i≤j≤n) and changing the array a1,a2,…,an to a1,a2,…,ai−1,aj,aj−1,…,ai,aj+1,…,an−1,an. The length of the subarray is then j−i+1.

Input

The first line contains a single integer t (1≤t≤100) — the number of test cases.

Each test case consists of two lines.

The first line of each test case contains two integers n and k (1≤k≤n≤100) — the number of boxes and the length of the maximum reverse that Theofanis can make.

The second line contains n integers a1,a2,…,an (1≤ai≤109) — the number written on each box.

Output

For each test case, print YES (case-insensitive), if the array can be sorted in non-decreasing order, or NO (case-insensitive) otherwise.

Example

Input

5
3 2
1 2 3
3 1
9 9 9
4 4
6 4 2 1
4 3
10 3 830 14
2 1
3 1

Output

YES
YES
YES
YES
NO

```
1. import java.util.*;
2. public class Main{
3. public static boolean isSorted(int[] arr,int n,int k){
4. for(int i=0;i<(n-1);i++){
5. if(arr[i]>arr[i+1]) return false;
6. }
7. return true;
8. }
9. public static void main(String args[]){
10. Scanner x = new Scanner(System.in);
11. int t = x.nextInt();
12. while((t--)!=0){
13. int n = x.nextInt();
14. int k = x.nextInt();
15. int arr[] = new int[n];
16. for(int i=0;i<n;i++){
17. arr[i] = x.nextInt();
18. }
19. if(k>=2) System.out.print("YES");
20. else{
21. boolean result = isSorted(arr,n,k);
22. if(result) System.out.print("YES");
23. else System.out.print("NO");
24. }
25. }
26. }
27. }
```

1900A
1901A
1878A
A. Game with integers
A. how much does
1890A:
Concepts learned:
	1] we cannot access a hash map using index ---> instead use list of values in the hashmap ---> List<Integer> list = new ArrayList<>(Map.values());

1877A
1896A
1859A
1866A
1862B

Xsquare and Double Strings
swap and delete - 1913B
1883C
1859B
1849B
ski resort
1791D
1765M
beautiful array
basket ball togather
1632B