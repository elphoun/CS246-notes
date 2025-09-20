In C, we dealt with `malloc`. **WE WILL NOT BE USING THIS IN C++**
```C
int *p  = malloc(n * sizeof(int));
...
free(p)
```

Instead, use `new` and `delete`. This is a more type-aware and less error-prone alternative.
```C++
struct Node {...};
Node *p = new Node;
...
delete p;
```

Updated Rules of Memory
---
* All local variables reside on the **stack**
* Variables are deallocated when they are popped off the stack or go out of scope.
* Allocated memory remains in the heap, and remains allocated until `delete` is called. Memory allocated with `new` must be freed with `delete`
* If the memory is not deleted, then it will cause a **memory leak** and the program will eventually fail. 
* Memory allocated with `new _[_]` must be freed with `delete[]`
* Combining these will result in **undefined behavior**

Note that all of these changes still apply to arrays. 
```C++
Node *nodeArray = new Node[10];
...
delete [] nodeArray;
```