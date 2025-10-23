**NullPTR** (`nullptr`) is similar to void pointers in C
```C++
Node n {5, nullptr}; // Neither NULL nor 0 should be used
const Node n2 = n; // Immutable copy of n
```

The reason `nullptr` is used is because it avoids ambiguous cases in C where `NULL` is equal to `0`. Instead, `nullptr` is automatically converted to any pointer type. This avoids the ambiguous cases. 

```C++
// since NULL can be equal to 0, the compiler will not know which version of f to call
void f(Node *p);
void f(int x);
```

**Note that running `delete nullptr;`** is a safe call and does nothing.