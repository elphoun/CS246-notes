C++ has another pointer-like type known as **references**.  
* These are similar to constant pointers with automatic dereferencing. 
```C++
cin >> x; // correct
cin >> &x; // incorrect
```

References are the reason why `cin >> x` works. 
```
istream &operator >> (istream &in, int &n);
```
The stream uses a reference because reading large arguments is an expensive process.
```C++
struct ReallyBig {};
int f (ReallyBig rb) {}; // slow call since it copies each time
int g (ReallyBig &rb) {}; // much faster call since it takes the address from memory
int h (const ReallyBig &rb) {}; // simialrly fast call but guarantees that h does not change
```

References behave exactly like their reference. This means that the the reference is an **alias** for y
```C++
int y = 10;
int *const z = &y; 
z = 12 // changes the value of y
int *p = &z; // p is not the address of y
```

If the `&` is being used to declare a variable, then it is a reference. If it appears in an expression, then it is an address of another variable. 
```C++
int &z; // declaring a reference
int *p = &z; // declaring an address 
```

**l-values** are expressions that refer to to memory locations.
* References can be passed as function parameters
```C++
void inc(int &n) { ++n; } // sets up n as a reference with the &

int x = 5;
inc(x);
cout << x; // prints 6
```

**Restrictions**
* cannot be left uninitialized
* Must be initialized to something with a valid address (an l-value)
```C++
// invalid
int &x = 3;
int &x = y + z;

// valid
int &x = y;
```
* create a pointer to a reference. The other way around is ok though.
	* This also means an array of references cannot be created
```C++
// invalid
int &*x;
int &r[3] ={__, __, __}; 
// valid
int *&x = __; 
```