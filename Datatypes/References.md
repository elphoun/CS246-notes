**References** are a pointer-like type exclusive to C++. They are similar to constant pointers with automatic dereferencing, so changes applied to the reference also reflect on the original variable.

```C++
// References are defined using the '&' symbol
int x = 10;
int& ref = x;

// note that this '&' is different from the one used to get variable addresses for pointers.
int &z; // declaring a reference
int *p = &z; // declaring an address
```

References are the reason why `cin >> x` works. The stream uses a reference to lessen the cost of reading large inputs.

```C++
istream &operator>>(istream &in, int &n);

cin >> x; // correct call
cin >> &x; // incorrect call

struct ReallyBig {};
int f (ReallyBig rb) {}; // slow call since it copies each time
int g (ReallyBig &rb) {}; // much faster call since it takes the address from memory
int h (const ReallyBig &rb) {}; // simialrly fast call but guarantees that h does not change
```

References are not always stored in memory but behave exactly like the variable they are referencing which could be in memory. This means that the the reference is an **alias** for y.

```C++
int y = 10;
int *const z = &y;
z = 12 // changes the value of y
int *p = &z; // p is not the address of y
```

Since references and addresses both use `&`, the exact meaning of the symbol can be determined based on the context
* if `&` appears in the type, then it is a reference
* if the `&` is part of an expression, then it is an address

```C++
void f(int &n); // reference
g(&x); // address
```

## L and R Values

**L-Values (Left-Values)** are expressions that have an identifiable location in memory, while **R-Values (Right Values)** are expression that have no identifiable location in memory. They are named after their placement when assigning values to variables.

- R-Values include integer/string values and returned values (i.e. `5`, `f();`)
- L-Values include variables and references (i.e. `int x`)

```C++
int a;
a = 1; // valid, since 'a' is a l-value

int b;
9 = b; // invalid, since 9 in a constant r-value that cannot be changed
```

In C++, R-Values are referenced by `&&` of type node. This is known as **R-Value reverence** and allows the compiler to temporarily reserve memory to put the value in.

```C++
int main() {
    int a = 10;
    int b = 20;
    // Since x + y is an R-Value, this is illegal since x + y is not in memory
    int &ref_to_rvalue = x + y;
    // use && to temporarily bind x + y into the r3 R-Value reference
    int&& r3 = a + b;
    // We can now use r3 to access the value, which is 30.
    std::cout << "The value of r3 is: " << r3 << std::endl;
    // The temporary object still exists because r3 is a reference to it.
    return 0;
}
```

```C++
struct Node {
	// move constructor
	Node (Node&& other): data{other.data}, next{other.next} {
		// removes the other's access to the remaining nodes so that the rest of the list does not get destroyed
		other.next = nullptr;
	}
}
```
## Restrictions of References

- References cannot be left uninitialized since they refer to a section of memory.
- References cannot reference another reference 
- References must be initialized to a L-value, or something with a valid address. They cannot be assigned to an R-value

```C++
// invalid
int &x = 3;
int &x = y + z;

// valid
int &x = y;
```

- References can refer to a pointer, but a pointer cannot point to a reference. This also means an array of references cannot be created

```C++
// invalid
int &*x;
int &r[3] ={__, __, __};
// valid
int *&x = __;
```

## In Functions

References can be passed as function parameters like pointers.

```C++
void inc(int &n) { ++n; } // sets up n as a reference with the &

int x = 5;
inc(x);
cout << x; // prints 6
```

Recall that defining and returning a pointer in a function that will be pushed off the stack means that the pointer will be invalid. The same logic applies to references.

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

Generally:

- By default, stick with **constant l-value reference**
- If changes should be reflected in the variable, then **pass by reference**
- If changes are small or you need to a temporary version of the argument, then **pass by value**
