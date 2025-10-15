In C++20, there is a more efficient way to compare objects of the same type with the **spaceship operator `<=>`**
* This is identical to `strcmp` in C
```C++
struct vec {
	int x, y;
	auto operator <=>(const vec& other) const {
		auto n = x <=> other.x; // compares x and other.x
		return (n == 0) ? (y <=> other.y) : n; // if n equals 0, then it returns the comparsion of y and other.y. Otherwise it returns n. 
	}
};
```