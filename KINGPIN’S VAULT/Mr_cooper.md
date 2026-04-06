er-diagram
dbms 
os
cn
cli app 
sql

1-easy , 1med ,1 hard or 2med
OOPS - BASIC

ddl and dml

# 1

Qns:

```
import java.util.regex.Pattern;
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner x = new Scanner(System.in);
        System.out.print("Enter str: ");
        String str = x.nextLine();
        System.out.print("Enter n: ");
        int n = x.nextInt();
        x.nextLine();
        int res = 0;
        List<String> list = new ArrayList<>();
        for(int i=0;i<n;i++) list.add(x.nextLine());
        for(int i=0;i<n;i++){
            String s = str;
            int count = 0;
            String search = list.get(i);
            if(!s.isEmpty()){    
                while(s.contains(search)){
                    s = s.replaceFirst(Pattern.quote(search),"");
                    count++;
                }
            res = Math.max(res,count);
            }
        }
        System.out.println(res);
    }
}
```

Y Pattern.quote ??

In this code, `Pattern.quote(sub)` is used to ensure that the substring `sub` is treated as a **literal string** rather than a **Regular Expression (Regex)**.

Here is the detailed breakdown of why it is necessary:

### 1. `replaceFirst` expects a Regex
The Java method `String.replaceFirst(String regex, String replacement)` interprets the first argument as a **Regular Expression**, not a plain text string.

*   **Regex:** A pattern language where characters like `.`, `*`, `+`, `?`, `(`, `)` have special meanings.
*   **Literal:** The actual characters typed by the user.

### 2. The Problem without `Pattern.quote`
If the user inputs a string containing special regex characters, the code will behave incorrectly or crash.

**Example 1: The Dot (`.`)**
*   **User Input:** `.`
*   **Without `quote`:** `replaceFirst(".", "")`
    *   In Regex, `.` matches **any single character**.
    *   **Result:** It will delete the first character of the string, regardless of whether it is a dot or not.
*   **With `quote`:** `replaceFirst("\\Q.\\E", "")`
    *   **Result:** It will only delete the literal dot character `.`.

**Example 2: The Asterisk (`*`)**
*   **User Input:** `*`
*   **Without `quote`:** `replaceFirst("*", "")`
    *   In Regex, `*` is a quantifier (meaning "0 or more of the previous element"). It cannot appear at the start of a pattern.
    *   **Result:** The code will throw a `java.util.regex.PatternSyntaxException` and crash.
*   **With `quote`:** It treats `*` as a normal text character and works fine.

### 3. How `Pattern.quote` works
`Pattern.quote(sub)` wraps the input string with `\Q` and `\E`.
*   `\Q` starts "quote mode" (literal mode).
*   `\E` ends "quote mode".

If `sub` is `a.b`, `Pattern.quote(sub)` returns `\Qa.b\E`.
When passed to `replaceFirst`, the Regex engine sees `\Qa.b\E` and knows to ignore the special meaning of the `.` and match it literally.

### Summary
You use `Pattern.quote(sub)` to **prevent errors and logic bugs** caused by special characters (like `.`, `*`, `+`, `[`, etc.) in the user's input, ensuring the code 
searches for the exact text provided.


```
import java.util.*;

class Main {

    static class DSU {
        int[] parent, rank;
        int components;

        DSU(int n) {
            parent = new int[n];
            rank = new int[n];
            components = n;

            for (int i = 0; i < n; i++)
                parent[i] = i;
        }

        int find(int x) {
            if (parent[x] != x)
                parent[x] = find(parent[x]); // path compression
            return parent[x];
        }

        void union(int a, int b) {
            int pa = find(a);
            int pb = find(b);

            if (pa == pb) return;

            if (rank[pa] < rank[pb]) {
                parent[pa] = pb;
            } else if (rank[pb] < rank[pa]) {
                parent[pb] = pa;
            } else {
                parent[pb] = pa;
                rank[pa]++;
            }

            components--;
        }
    }

    public static int solve(int N, int K, int[][] logs) {
        Arrays.sort(logs, Comparator.comparingInt(a -> a[0]));

        DSU dsu = new DSU(N);

        for (int[] log : logs) {
            int day = log[0];
            int a = log[1];
            int b = log[2];

            dsu.union(a, b);

            if (dsu.components == 1)
                return day;
        }

        return -1;
    }
}
```