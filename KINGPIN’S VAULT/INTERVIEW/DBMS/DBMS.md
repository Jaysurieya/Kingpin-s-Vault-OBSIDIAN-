Functional dependency: [Youtube](https://www.youtube.com/watch?v=dR-jJimWWHA&list=PLdo5W4Nhv31b33kF46f9aFjoJPOkdlsRc&index=4)
- x -> y
- x is dependent on y [so it does not mean using x we can compute / find y without any tables] 
- so only using a table we can find y using x 

- if x-> y , t1.x = t2.x then t1.y must be = to t2.y


## Types of functional dependency: [Youtube](https://www.youtube.com/watch?v=eIH7zRVelnw&list=PLdo5W4Nhv31b33kF46f9aFjoJPOkdlsRc&index=6)

- Trivial
- Non trivial
- multi valued
- transitive

==In all  these x and y may be a single or set of attributes==
### Trivial :
x->x
x->y , then y is subset of x [eg:(name, roll_no)-> name ---> this definitely valid]

### Non trivial :
x->y and y is not a subset of x [we need to check weather it is a FD or not]

### Semi trivial :
x->y and y is partial subset of x [eg : (name, mark) -> (mark , age)]


## Rules for Functional Dependency :

- Reflexivity : (same as trivial)
		x->x
		x->y , then y is subset of x [eg:(name, roll_no)-> name ---> this definitely valid]
- Transitivity :
		if (x->y) and (y->z) then x->y
		eg : 
		![[Pasted image 20260310050518.png]]
		here (name --> marks) and (marks --> dept) then (name-->dept) 
- Augumentation : 
		if(x->y) then (xa->ya) is a valid FD [eg: name -> marks then (name,course)->(marks,course)]
- Union  : if x->y and x->z then we can write x->y,z
- Decomposition : x -> yz then x->y and x->z is valid [eg: xy->ab then xy->a and xy->b is valdi but x->ab is not valid]
- Composition : x->y and a->b then (xa->yb is valid)
- pseudo transitivity : if(x->y) and (yz->a) then (xz->a is valid by ):
[x->y ---> xz->yz and yz -> a now by transitivity xz-> a is valid ]

## Attribute Closure:
x^+ ---> contains all the attributes determined by x or all the attributes  that are functionally dependent on x

in case of attribute closure --> ==super key is some thing whose attribute closure contains all the given attributes==

> [!tip]+ in case of attribute closure  what is super key:
> super key is some thing whose attribute closure contains all the given attributes


