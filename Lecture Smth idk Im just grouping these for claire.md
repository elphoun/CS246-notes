**Recall:** Pass-by-const-ref over pass-by-value for anything larger than a pointer. Unless the function needs to make a copy anyways, in which case use pass-by-value

```C++
int f(int &n); 
f(5); // invalid since 5 is not a valid memory location. The passed reference must be an l-value. You cannot change "5".

int g(const int &n);
g(5); // valid since n cannot be changed. 5 is stored in a temporary location on the stack so there is an l-value to point to
```

In the case of `istream foperator >> (istream &in, int &n);` the stream is being passed and returned by reference to save copying. This is important because stream variables are not allowed to be copied. 

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

Memory:
* All local variables reside on the **stack**
* Variables are deallocated when they are popped off the stack or go out of scope.
* Allocated memory remains in the heap, and remains allocated until `delete` is called. Memory allocated with `new` must be freed with `delete`
* If the memory is not deleted, then it will cause a **memory leak** and the program will eventually fail. 
* Memory allocated with `new _[_]` must be freed with `delete[]`
* Combining these will result in **undefined behavior**

**Arrays:**
```C++
Node *nodeArray = new Node[10];
...
delete [] nodeArray;
```

**Returning by Value/Pointer/Reference**
```C++
// this is a comparatively expensive call. n is copied to the stack on return
Node getMeNode() { 
	Node n;
	return n;
} 

// these are invalid. Never return the address or reference of a locally defined variable. 
Node *getMeNode() {
	Node n; //  pushed off the stack once returned
	return &n;
}
Node &getMeNode() {
	Node n; // references are the same. 
	return n;
}



```