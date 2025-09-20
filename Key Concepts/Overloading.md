**Overloading** is when the same function call is used for multiple implementations (i.e. `>>` in [[Input and Output]]).
* They must differ in either the **number** or **type** of arguments.

In C, each datatype needs its own separate function.
```C
int negInt(int n) {return -n;}
bool negBool(bool b) {return !b;}
```

In C++, you can use the `operator` keyword to overload function calls for different types.
```C++
struct Vec {
	int x, y;
};

Vec operator+(const Vec &v1, const Vec &v2) {
	Vec v { v1.x + v2.x, v1.y + v2.y };
	return v;
}

/*
Note the different types of k and v. This means you might need to define a new version of this that supports the other way around
To save code and prevent sync issues. We use the original operator. 
*/
Vec operator*(const int k, const vec &v) { 
	return {k * v.x, k * v.y};
}
Vec operator*(const vec &v, const int k) { 
	return k * v;
}
```

A special case of this comes with the `>>` and `<<` operators.
```C++
struct Grade {
	int grade;
};

ostream &operator<<(ostream &out, const Grade &g) {
	out << g.grade << '%';
	return out;
}
istream &operator>>(istream &in, grade &g) {
	in >> g.grade;
	if (g.grade < 0) {g.grade = 0;} 
	else if (g.grade > 100) {g.grade = 100;}
	return in;
}
```