## Differences

Imports are in the format `import <iostream>; ` instead of `#include <library_name>`.

- The lack of `#` indicates that we are not doing anything with the preprocessor

```C
// example in C
#include <stdio.h>
```

```C++
// example in C++
import <iostream>;
```

`using namespace std` is used as an alternative to `std::`. This is similar to how libraries in python support either directly importing the function or using the format `<module>.<func_name>`

Most code in C can be directly translated over to C++. However, the conventions that C++ uses are different from the ones in C.

- In C, Hello World can be printed like:

```C
#include <stdio.h>
int main() {
	printf("Hello World\n");
	return 0;
}
```

- This is also valid in C++, but not all code in C can be directly transferred to C++

```C++
import <iostream>;
using namespace std;

int main() {
	cout << "Hello World!" << endl;
	return 0;
}
```

## Similarities

`main` must return an `int`, not `void`. The operating system calls `main`, and the return value is a status code used to indicate if a program has succeeded or not.

- `0` indicates that the program is valid
- The `return` can be omitted from main by default, it will automatically return `0`

In C++, strings can be printed using a **buffer system**. The buffer system improves performance by delaying the output until all of the characters/content to print can be transferred out.

```C++
std::cout << ___ << ___ << ... // Adds item to the buffer system.
std::endl = end-of-line // Prints the end of line and flushes the buffer (forces all items to print)
```

**Pass-by-value** receives a copy of x and mutates the copy. The original **does not change**.

```C++
// Example of pass-by-value
void inc(int n) { ++n; }

int x = 5;
inc(x);
cout << x; // outputs 5 since inc neither mutates the original value nor returns the value to mutate.
```

**Pass-by-reference** receives a copy of x's pointer and mutates the value inside the pointer. The original changes.

```C++
// Example of pass-by-reference
void inc(int *n) { ++*n; }

int x = 5;
inc(&x);
cout << x; // outputs 6 since inc mutates the value associated with the pointer.
```

**Pass-by-const-ref** is preferred over **pass-by-value** for anything larger than a pointer. Unless the function needs to make a copy anyways, in which case use pass-by-value.

```C++
int f(int &n);
f(5); // invalid since 5 is not a valid memory location. The passed reference must be an l-value. You cannot change "5".

int g(const int &n);
g(5); // valid since n cannot be changed. 5 is stored in a temporary location on the stack so there is an l-value to point to
```

In the case of `istream foperator >> (istream &in, int &n);` the stream is being passed and returned by reference to save copying. This is important because stream variables are not allowed to be copied.

