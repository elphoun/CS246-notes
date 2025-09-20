Programs should be split into **modules**. Each file should contain

- **interface:** Type definitions and headers for the functions.
- **implementation:** Full definition of the program and each provided function in the interface

There are 2 ways to create variables:

- **Declaration:** Asserts existence of a variable
- **Definition:** Declares a variable and assigns it a value

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
