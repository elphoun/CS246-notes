**NullPTR** (`nullptr`) is similar to void pointers in C
```C++
Node n {5, nullptr}; // Neither NULL nor 0 should be used
const Node n2 = n; // Immutable copy of n
```

**Note that running `delete nullptr;`** is a safe call and does nothing. 