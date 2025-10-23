**Constants** have values that cannot be mutated through the program's execution. Any changes made to a constant will result in a compilation error

```C++
const int MAX_GRADE = 100;
```

Structures also cannot be changed

```C++
const Node{5, nullptr}
```





Calling methods are valid, but they cannot modify fields

```C++
struct Student {
	int assns, mt, final;
	float grade() const; // the const at the end indicates that this method does not modify fields.
};
```