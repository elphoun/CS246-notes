**Recall:** We prefer **pass-by-const-ref** over **pass-by-value** for anything larger than a pointer. Unless the function needs to make a copy anyways, in which case use pass-by-value.


```C++
int f(int &n); 
f(5); // invalid since 5 is not a valid memory location. The passed reference must be an l-value. You cannot change "5".

int g(const int &n);
g(5); // valid since n cannot be changed. 5 is stored in a temporary location on the stack so there is an l-value to point to
```

In the case of `istream foperator >> (istream &in, int &n);` the stream is being passed and returned by reference to save copying. This is important because stream variables are not allowed to be copied. 

---
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
/* 
this is a comparatively expensive call. n is copied to the stack on return 
This is still the one you should pick though (so idk why he went through so many iterations) but will cover in later class. 
*/
Node getMeNode() { 
	Node n;
	return n;
} 

/* these are invalid. Never return the address or reference of a locally defined variable */
Node *getMeNode() {
	Node n; //  pushed off the stack once returned
	return &n;
}
Node &getMeNode() {
	Node n; // references are the same. 
	return n;
}

/*
It is ok for an operator (i.e. >>) to return an istream& because the variable is not local. 
In other words, references can be returned from a function as long as it was not defined in the function's callstack
This iteration of getMeNode returns a pointer to heap data, meaning it is still alive
It still needs to be deleted after use
*/ 
Node *getMeNode(){
	return new Node();
}
```

---
**Operator Overloading**
C++ gives meaning to operators for different types
```C++
struct Vec {
	int x, y;
};

Vec operator+(const Vec &v1, const Vec &v2) {
	Vec v { v1.x + v2.x, v1.y + v2.y };
	return v;
}

/*
note the different types of k and v. This means you might need to define a new version of this that supports the other way around
To save code and prevent sync issues. We use the original operator. 
*/
Vec operator*(const int k, const vec &v) { 
	return {k * v.x, k * v.y};
}
Vec operator*(const vec &v, const int k) { 
	return k * v;
}
```

A special case of this comes with the `>>` and `<<` operators. For instance:
```C++
struct Grade {
	int grade;
};

ostream &operator<<(ostream &out, const Grade &g) {
	out << g.grade << '%';
	return out;
}
istream &operator>>(istream &in, grade &g) {
	in >> g.grade;
	if (g.grade < 0) {g.grade = 0;} 
	else if (g.grade > 100) {g.grade = 100;}
	return in;
}
```

---
**Seperate Compilation**
Programs should be split into **modules**. Each file should contain
* **interface:** Type definitions and headers for the functions.
* **implementation:** Full definition of the program and each provided function in the interface

There are 2 ways to create variables:
* **Declaration:** Asserts existence of a variable
* **Definition:** Declares a variable and assigns it a value

```C++
// in the interface file (vec.cc)
export module vec; // indicates that this is the module interface
export struct Vec {
	int x, y;
};
export Vec operator+(const Vec &v1, const Vec &v2);

// in the main file (main.cc)
import vec;
int main() {
	Vec v {1, 2};
	v = v + v;
	...
}
```
