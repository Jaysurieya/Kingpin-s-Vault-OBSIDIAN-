# Sorted Problems (Easy → Hard)

## Level 1 — Very Easy (Warm-up)

Basic loops, conditions, simple string/array work.

1. **Reverse the String**
    
2. **Modify the Array (add index)**
    
3. **Sensor Reading**
    
4. **Majority Element**
    
5. **Split the Number**
    
6. **Add 1 to the Number**
    
7. **Convert Binary → Hexadecimal**
    
8. **Add Two Binary Numbers**
    
9. **Balanced Parentheses**
    
10. **Number Conversion (Decimal/Binary/Hex/Octal)**
    

---

# Level 2 — Easy

Requires slightly better array/string manipulation.

11. **Sort using Frequency**
    
12. **XOR Array (minimum XOR pair)**
    
13. **Find Pair (min difference + max index diff)**
    
14. **Rotate Matrix 90°**
    
15. **First Repeating in Every Row**
    
16. **ATM Denomination**
    
17. **Sort the String (remove duplicates ignoring case & special chars)**
    

---

# Level 3 — Medium

Needs algorithmic thinking.

18. **Next Smallest Number using Same Digits**
    
19. **Missing Number in Consecutive String**
    
20. **Merge Intervals**
    
21. **Longest Increasing Sequence (remove subarray)**
    
22. **Largest Subarray with Equal Frequency**
    
23. **Word Exists in Grid (DFS)**
    

---

# Level 4 — Hard

Advanced logic / parsing / data structures.

24. **Largest Possible Palindrome**
    
25. **Even Length Largest Palindrome**
    
26. **Normalize Array**
    
27. **Maximum Area Triangle with Different Colors**
    
28. **Password Validation System**
    
29. **AVL Tree Construction + Leaf Nodes**
    
30. **Numerical Standard Conversion (Indian / US / German)**



```
#include <stdio.h>
#include <string.h>
int main()
{
    int n;
    printf("Enter N: ");
    scanf("%d",&n);
    getchar();
    char arr[n+1];
    fgets(arr,sizeof(arr),stdin);
    for(int i=n-1;i>=0;i--) printf("%c",arr[i]);
    return 0;
}
```

using strrev: find

```
#include <stdio.h>

int main()
{
    int n;
    scanf("%d",&n);

    int arr[n];

    for(int i=0;i<n;i++)
        scanf("%d",&arr[i]);

    for(int i=0;i<n;i++)
    {
        arr[i] = arr[i] + i;

        if(arr[i] > 99999)
            arr[i] = arr[i] % 100000;
    }

    for(int i=0;i<n;i++)
        printf("%d ",arr[i]);

    return 0;
}
```


## majority element :

```
#include <stdio.h>
#include <string.h>
int main()
{
    int nums[] = {1,1,2,2};
    int can = nums[0],count = 1;
    int n = sizeof(nums)/sizeof(nums[0]);
    for(int i=1;i<n;i++){
        if(count==0){
            can = nums[i];
            count=1;
        }
        if(can==nums[i]) count++;
        else count--;
    }
    int freq = 0;
    for(int i=0;i<n;i++) if(nums[i]==can) freq++;
    if(freq>(n/2)) printf("%d",can);
    else printf("no majority");
    return 0;
}
```

## Add 1 to the int:
```
#include <stdio.h>
#include <string.h>
int main()
{
    int nums[] = {1,1,2,2};
    long x = 0;
    for(int i=0;i<4;i++) x=(x*10)+(nums[i]);
    x+=1;
    printf("%ld",x);
    return 0;
}
```


## Sensor Reading: ---> simple

```
#include <stdio.h>

int main()
{
    int n;
    scanf("%d",&n);

    int low, high;
    scanf("%d %d",&low,&high);

    int arr[n];

    for(int i=0;i<n;i++)
        scanf("%d",&arr[i]);

    int count = 0;
    int idx[n];

    for(int i=0;i<n;i++)
    {
        if(arr[i] < low || arr[i] > high)
        {
            idx[count] = i;
            count++;
        }
    }

    if(count == 0)
    {
        printf("Safe");
    }
    else if(count > n/2)
    {
        printf("Critical\n");

        for(int i=0;i<count;i++)
            printf("%d ",idx[i]);
    }
    else
    {
        printf("Warning\n");

        for(int i=0;i<count;i++)
            printf("%d ",idx[i]);
    }

    return 0;
}
```

