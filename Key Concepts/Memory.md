In C, we dealt with `malloc`. This should not be used in C++20 convention. 
```C
int *p  = malloc(n * sizeof(int));
...
free(p)
```

Instead, use `new` and `delete`. This is a more type-aware and less error-prone alternative.
* Memory allocated with `new` still needs to be freed with `delete`
* There is no alternative to `realloc`, memory will need to be manually copied over 
* Arrays can be defined with `new[]` and must be deleted with `delete[]`. Mixing will lead to undefined behavior
```C++
struct Node {...};
Node *p = new Node;
...
delete p;
```