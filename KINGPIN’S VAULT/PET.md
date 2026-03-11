# C inbuilt functions:
## Maths:

abs
sqrt
ceil
floor
pow(int,power_val)

## character functions:

isupper
islower
toupper
tolower
isalpha
isdigit

## String:

strlen
strcpy
strcat
strncat
strcmp
fgets
fputs

---
## memory functions:

malloc => int * a = (int * ) malloc (no * sizeof(int));  
calloc => int * b = (int * ) calloc (no ,sizeof(int));
realloc => a = (int * ) realloc (a,no * sizeof(int));
free => free(a);

---
## Trees:

> [!example]+ Binary Tree Construction (C programming) : [with traversals]
> ```
> #include <stdio.h>
> #include <stdlib.h>
> 
> struct Node{
>   int data;
>   struct Node* left;
>   struct Node* right;
> };
> 
> struct Node* create(int value){
>     struct Node* root = (struct Node*)malloc(sizeof(struct Node));
>     root->data = value;
>     root->left = NULL;
>     root->right = NULL;
>     return root;
> }
> 
> struct Node* insert(int value,struct Node* root){
>     if(root==NULL) return create(value);
>     
>     if(value < root->data) root->left = insert(value,root->left);
>     else root->right =  insert(value,root->right);
>     return root;
> }
> 
> void inorder(struct Node* root){
>     if(root == NULL) return;
>     
>     inorder(root->left);
>     printf("%d ",root->data);
>     inorder(root->right);
> }
> 
> void preorder(struct Node* root){
>     if(root == NULL) return;
>     
>     printf("%d ",root->data);
>     preorder(root->left);
>     preorder(root->right);
> }
> 
> 
> void postorder(struct Node* root){
>     if(root == NULL) return;
> 
>     postorder(root->left);
>     postorder(root->right);
>     printf("%d ",root->data);
> }
> 
> struct Node* search(struct Node* root, int key){
>     if(root==NULL) return NULL;
>     if(root->data==key) return root;
>     if(key<root->data) return search(root->left,key);
>     else return search(root->right,key);
> }
> 
> 
> void main(){
>     int n,val;
>     printf("Enter n: ");
>     scanf("%d",&n);
>     struct Node* root = NULL;
>     for(int i=0;i<n;i++){
>         scanf("%d",&val);
>         root = insert(val,root);
>     }
>     
>     printf("inorder:");
>     inorder(root);
>     printf("\n");
>     
>     printf("preorder:");
>     preorder(root);
>     printf("\n");
>     
>     printf("postorder:");
>     postorder(root);
>     printf("\n");
>     
>     printf("Address of 5 : ");
>     struct Node* res = search(root,5);
>     if(res != NULL)printf("%p : %d\n", (void*)res, res->data);
>     else printf("Not found\n");   
> }
> ```


## Stack in java:

> [!example]+ Stack Implementation and operations [using java]
> ```
> import java.util.*;
> 
> class Main{
>     public static void main(String args[]){
>         Stack<Integer> st = new Stack<>();
>         Deque<Integer> de = new ArrayDeque<>();
>         for(int i=0;i<3;i++){
>             st.push(i);
>             de.push(i);
>         }
>         st.pop();
>         de.pop();
>         System.out.println(de.peek());
>         System.out.println(st.peek());
>         
>         // acessing via index
>         System.out.println(st.get(0));
>         System.out.println(st.indexOf(1));
>     }
> }
> ```

## Difference Between Stack and Deque:

> [!warning]+ Deque vs Stack
> ✅ Core difference: Stack = List-like, Deque = Queue-like
> ✅ Stack in Java:
> 
> Stack is a class that extends Vector
> 
> Vector is basically a dynamic array
> 
> So elements are stored like:
> 
> [10, 20, 30, 40]
>  0   1   2   3
> 
> 
> ✅ That’s why it supports:
> 
> get(index)
> 
> indexOf(value)
> 
> random access
> 
> So Stack has index support because it’s built on an array list structure (Vector).
> 
> ✅ Deque (ArrayDeque):
> 
> Deque is a double-ended queue
> 
> It is designed for:
> 
> insert/remove at front
> 
> insert/remove at rear
> 
> internally it works like a circular buffer
> 
> Example:
> 
> capacity: 8
> indexes:  0 1 2 3 4 5 6 7
> data:       30 40 50
> head -> 2
> tail -> 5
> 
> 
> Here:
> 
> the “first element” is not always at index 0
> 
> elements may wrap around (circular)
> 
> So there is no fixed direct indexing like arr[i].

## Linked List Creation in JAVA:

> [!example]+ Linked List:
> ```
> import java.util.*;
> 
> public class Main {
>     public static void main(String[] args) {
> 
>         LinkedList<Integer> list = new LinkedList<>();
> 
>         // ✅ Add
>         list.add(10);               // add at end
>         list.add(20);
>         list.addFirst(5);           // add at beginning
>         list.addLast(30);           // add at end
>         list.add(2, 15);            // add at index
> 
>         System.out.println(list);   // [5, 10, 15, 20, 30]
> 
>         // ✅ Access
>         System.out.println(list.get(0));      // 5
>         System.out.println(list.getFirst());  // 5
>         System.out.println(list.getLast());   // 30
> 
>         // ✅ Update
>         list.set(1, 99);  // replace index 1 value with 99
>         System.out.println(list);   // [5, 99, 15, 20, 30]
> 
>         // ✅ Remove
>         list.remove();          // removes first
>         list.removeFirst();     // removes first
>         list.removeLast();      // removes last
>         list.remove(1);         // removes by index
> 		 // list.remove(Integer.valueOf(20));
> 
>         // ✅ Search
>         System.out.println(list.contains(20));   // true/false
>         System.out.println(list.indexOf(20));    // index or -1
> 
>         // ✅ Size
>         System.out.println(list.size());
> 
>         // ✅ Traverse
>         for (int x : list) {
>             System.out.print(x + " ");
>         }
>     }
> }
> ```

> [!example]+ Queue:
> ```
> import java.util.*;
> 
> public class Main {
>     public static void main(String[] args) {
>         Queue<Integer> q = new LinkedList<>();
> 
>         // insert
>         q.add(10);
>         q.add(20);
>         q.add(30);
> 
>         // front element
>         System.out.println(q.peek());   // 10
> 
>         // remove
>         System.out.println(q.remove()); // 10
> 
>         System.out.println(q);          // [20, 30]
>     }
> }
> 
> ```


> [!todo]+ PET:
> 
> 1. Push and pop operations
> 2. Min,Max,Sum,Avg in an array ( array size not fixed)
> 3. Print odd and even position numbers ( array size not fixed)
> 4. Insertion, deletions, search in a linked list
> 
> 10marks
>  
> 1. Construct Binary search tree and perform binary search 
> 2. Same question

