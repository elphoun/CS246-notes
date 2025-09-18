## Differences in C and C++:
* Imports are in the format `import <iostream>; ` instead of `#include <library_name>`. 
	* The `#` indicates that we are not doing anything with the preprocessor
	* `iostream` is a module that imports the default input/output streams
* `using namespace std` is used as an alternative to `std::`. This is similar to how libraries in python support either directly importing the function or using the format `<module>.<func_name>` 
## Important Notes:
* `main` must return an `int`, not `void`. The operating system calls `main`, and the return value is a status code used to indicate if a program has succeeded or not.
	* `0` indicates that the program is valid
	* The `return` can be omitted from main by default, it will automatically return `0`

In `C`, `"Hello World"` can be printed like:
```C
#include <stdio.h>
int main() {
	printf("Hello World\n");
	return 0;
}
```

This is also valid in C++, but not all code in C can be directly transferred to C++ 
```C++
import <iostream>;
using namespace std;

int main() {
	cout << "Hello World!" << endl;
	return 0;
}
```

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
