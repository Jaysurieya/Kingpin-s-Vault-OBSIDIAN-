- Length of a linked list
- middle of a linked list
- happy no [slow fast approach]
- find duplicate [slow fast approach]
- LC 141
- LC 142
- LC 273
- LC 2095
- LC 19
- LC 202
- LC 203
- GFG delete the last occurance
- LC 83
- LC 82
- LC 376
- reverse the list LC 206:
```
using stack: 

Stack<ListNode> stack = new Stack<>();
        ListNode current = head;
        if(head==null) return null;
        while(current!=null){
            stack.push(current);
            current = current.next;
        }
        ListNode newhead = stack.pop();
        current = newhead;
        while(!stack.empty()){
            current.next = stack.pop();
            current = current.next;
        }
        current.next = null;
        return newhead;
```
- LC 92
- LC 25
- LC 24
- LC 2047
- LC 234
- LC 61
- LC 2
- LC 445
- LC 369
- LC 21
- LC 23
- LC 160
- LC 143
- LC 328
- LC 86
- LC 138
- LC 141
- reverse the list in k groups
	- LC 25

Backtracking:
LC 17

middle of a linked list: 
use slow and fast pointer approach
```
class Solution {
    public ListNode middleNode(ListNode head) {
        ListNode slow, fast;
        slow = fast = head;
        while(fast!=null && fast.next!=null ){
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
}
```
==Note:  fast != null should come first followed by fast.next != null, becoz first the node should not be equal to null then only we can check for fast.next ==

Happy number:
slow fast pointer approach:
```
class Solution {
    public boolean isHappy(int n) {
        int slow = n,fast = n;
        do{
            slow = sumofsquares(slow);
            fast = sumofsquares(sumofsquares(fast));
        }while(fast!=1 && slow!=fast);
        if(slow==fast && fast!=1) return false;
        else return true;
    }
    public int sumofsquares(int num){
        int res = 0;
        while(num!=0){
            int x = num%10;
            res += (x*x);
            num/=10;
        }
        return res;
    }
}
```

==Note : i] use do_while , ii] [slow == fast && fast!=1] ---> if n=1 should be handled ==

LC 141:
finding cycles in a linked list: [slow fast pointer approach]
```
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head==null || head.next==null || head.next.next==null) return false;
        ListNode slow , fast;
        slow = fast = head;
        do{
            slow = slow.next;
            fast = fast.next.next;
        }while(fast!=null && fast.next!= null && fast!=slow);
        if(fast==null || fast.next==null) return false;
        else return true;
    }
}
```

LC 142:
	slow fast approach : 
```
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if(head==null || head.next==null || head.next.next==null) return null;
        ListNode slow ,fast;
        slow = fast = head;
        do{
            slow=slow.next;
            fast=fast.next.next;
        }while(fast!=null && fast.next!=null && slow!=fast);
        if(fast==null || fast.next==null) return null;
        else{
            slow = head;
            while(slow!=fast){
                slow = slow.next;
                fast = fast.next;
            }
            return slow;
        }
    }
}
```

LC 2095:
```
class Solution {
    public ListNode deleteMiddle(ListNode head) {
        if(head==null) return null;
        ListNode slow ,fast;
        slow = fast = head;
        ListNode prev = null;
        while(fast!=null && fast.next!=null){
            prev = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        if(prev==null) head = null; 
        else prev.next = prev.next.next;
        return head;   
    }
}
```