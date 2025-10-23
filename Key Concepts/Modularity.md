There are 2 ways to create variables:

- **Declaration:** Asserts existence of a variable
- **Definition:** Declares a variable and assigns it a value

Programs are be split into **modules** with multiple files fulfilling different purposes. Each **module** should be split into the follow 2 type of files:

- **interface:** Type definitions and headers for the functions.
	- To export the module, use the syntax `export module <module>`
	- All functions and classes should have the `export` keyword
- **implementation:** Full definition of the program and each provided function in the interface
	- To indicate that the module is in this file, use the syntax `module`

In C++20, there is a new convention for the interface file. Instead of `.h`, we still use `.cc`
```C++
// in the interface file (vec.cc), used to be .h
export module vec; // indicates that this is the module interface
export struct Vec {
	int x, y;
};
export Vec operator+(const Vec &v1, const Vec &v2);

// In the implementation file (vec-impl.cc)
module vec;
vec operator+(const vec& v1, const vec& v2) {
	return { v1.x + v2.x, v1.y + v2.y; }
}

// in the main file (main.cc)
import vec;
int main() {
	Vec v {1, 2};
	v = v + v;
	...
}
```

## Importing and Compiling

**Object Files (`-o`)** are generated from a source `.cc` file using the command `-c`
```
g++20m -c <file_name>.cc
```

Files should be compiled in **dependency order**, so interface files, then implementation, then the main file. 
```
// Example from Before
g++20m -c vec.cc
g++20m -c vec-impl.cc
g++20m -c main.cc
```

