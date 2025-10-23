In C++20, there is a more efficient way to compare objects of the same type with the **spaceship operator `<=>`**. This is identical to `strcmp` in C, albeit with other types as well.
* `<=>` returns the `std::strong_ordering` type. Since this is a lot to type, the keyword  `auto`  is used instead. `auto` indicated **automatic type deduction**, meaning `n`'s type is whatever the returned value type is. 

```C++
import <compare>;

struct vec {
	int x, y;
	auto operator <=>(const vec& other) const {
		auto n = x <=> other.x; // compares x and other.x
		return (n == 0) ? (y <=> other.y) : n; // if n equals 0, then it returns the comparsion of y and other.y. Otherwise it returns n. 
	}
};
```

The keyword `default` is used to when the compiler knows how to compare an object's fields and the comparisons should be done in declaration order

```C++
struct vec {
	int x, y;
	auto operator<=>(const vec& other) const = default;
};
```

`default` is not appropriate when pointers are involved since addresses should not be compared.
```C++
struct Node {
	int data;
	Node* next;
	
	auto operator<=>(const Node& other) {
		auto n = (data <=> other.data);
		if ((n != 0) || (!next && !other.next)) return n;
        if (!next && other.next) return std::strong_ordering::less;
        if (next && !other.next) return std::strong_ordering::greater;
	}
};
```