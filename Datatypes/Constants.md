**Constants** have values that cannot be mutated through the program's execution

- Calling methods are valid, but they cannot modify fields

```C++
struct Student {
	int assns, mt, final;
	float grade() const; // the const at the end indicates that this method does not modify fields.
};
```