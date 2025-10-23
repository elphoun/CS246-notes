**Classes** are [[Structures]] with functions inside them.

- `s` is an **object** (particular instance of a class)
- `::` is the **scope resolution operator**, and defines a function in the context of a class
- `<class_name>::<func_name>` is a **member function** (method)

```C++
// in student.cc
export struct Student {
	int assns, mt, final;
	float grade(); // function to calculate grade
};
```

```C++
// in student-impl.cc
float Student::grade() { return 0.4 * assns + 0.2 * mt + 0.5 * final}
```

```C++
// in main.cc
Student s{60, 70, 80};
cout << s.grade << endl;
```

A big difference between classes defined with `class` and `struct` is that they are by default **private** rather than **public**. This is to enforce [[Invariants, Encapsulation, and Iterator Patterns]].

Notes:

- The keyword `this` is used in classes to disambiguate the field and the argument in certain scenarios.
- DFAs can be provided in a constructor to provide a default value for the field.

```C++
Student::Student(int assns = 0, int mt = 0, int final = 0) {
	this -> assns = assns;
	this -> mt = mt;
	this -> final = final;
}
```

# Member Initialization List (MIL)

The main benefit of constructors is that they allow field-by-field customization and therefore have more flexibility.

There are 2 types of fields:

- **Primative Fields:** Compiler provided fields that are left uninitialized (i.e. `bool`, `int`)
- **Object Fields:** Calls the object's default constructor (i.e. `class`, `struct`)

Note that all objects must be constructed before they are used. While primative fields can be directly supplied by the compiler.

The steps for object creation are:

1. Enough space is allocated.
2. Fields are initialized and the default constructor is called
3. Constructor body runs.

This means that the object fields are initialized BEFORE the default constructor is provided and called. Hence, having no default constructor and initializing a class with no arguments will lead to a **compilation error**

```C++
struct Basis {
	vec v1, v2;
}

Basis b; // error since v1 and v2 do not have a default constructor
```

We can resolve this issue with the **member initialization list (MIL)**. The MIL provides default values to to initialize the fields in step 2 instead of using the default constructor in step 3.

**NOTE:** the MIL is provided only in the interface file.

### Default Constructor

Default Constructors follows the syntax `<field_name>{<properties>} ...` in the MIL.

```C++
struct Basis {
	vec v1, v2;
	Basis(): v1{0, 1}, v2{1, 0} {} // default constructor
}
```

### Custom Constructor

A custom constructor is also typically provided for the user to customize their object. There are 2 ways to define this

```C++
// Option 1: Provding Default Values in the Default Constructor
struct Basis {
	vec v1, v2;
	Basis(): v1{0, 1}, v2{1, 0} {} // default constructor with default values provided
	Basis(const vec& v1, const vec& v2): v1{v1}, v2{v2} {}
};

// Option 2: Providing Default Values in Declaration
struct Basis {
	vec v1{1, 0}, v2{0, 1}; // default values provided in declaration
	Basis() {}; // default constructor
	Basis (const vec& v1, const vec&v2): v1{v1}, v2{v2} {}
}
```

Note that:

- The order of the fields does not matter
- The MIL is more efficient than setting values in the body since the overwrite step is saved

* The MIL MUST be used for object fields without default constructors, const fields, and reference fields.
* The MIL can be used for a fraction of the fields. The rest can be left default constructable.

# Big 5

The compiler provides the following for all classes. If one is written, then all 5 should probably be written too.

- Default Constructor (is not part of the big 5 and is overwritten when any of the other constructors are written)

* Destructor
* Copy Constructor
* Copy Assignment Operator
* Move Constructor
* Move Assignment Operator

Note that each object can only be constructed once. Attempting to construct multiple times is invalid. Each of the big 5 can also only be written once.

```C++
Student s1 { 60, 70, 80 };
Student s2 = s1; // copy constructor
Student s3;
s3 = s1; // invalid since only one constructor can be run for each Student.
```

## Destructors

**Destructors** are called by default when:

- Stack-allocated object is popped off the stack
- Heap-allocated object is deleted with `delete`
- Special method that runs when an object is destroyed (allows for deep destroys)

The steps for object destruction are (note that this is in opposite order of constructing):

1. Destructor body runs
2. Fields are deinitialized and destructor runs
3. Space is de-allocated (after 1 and 2)

Destructors are initialized with `~` at the beginning of its fieldname.

```C++
struct Node {
	...
	~Node() { delete next; } // recursively calls the destructor so no leak
	...
}
```

## Copying Objects

There are 2 methods provided when copying objects in each class.

The **copy constructor** defines how to make a new object as a copy of another

- In this version of the copy constructor, it makes a copy of the head `Node`, but assigns `next` to point at `other.next`. This means that changes made to either branch will affect the same linked list in memory.

```C++
struct Node {
	...
	// copy constructor (shallow)
	Node(const Node &other)
		: data{other.data},
		  next{other.next ? other.next : nullptr}
	{}

	// copy constructor
	Node (const Node &other)
		: data{other.data},
		  next{other.next ? new Node(*other.next) : nullptr} {}
	};
	...
}

// example of pointer differences
Node *n = new Node{1, new Node{2, new Node {3, nullptr}}};
Node p = *n;
Node *q = new Node{*n}; // note that n, p, and q point to the same node in memory, causing data to be shared between the lists.
```

- To resolve this, use `new Node(other.next)` to call the copy constructor again

The **copy assignment constructor** copies an existing object to another.

- Self assignment should be avoided by exiting the function if `other` is equal to `this`
- Any current objects should be deleted to avoid memory leaks
- `*this` is returned to maintain loops with recursive objects (i.e. `Node`)
- In the library `utility` there is a function called `swap`. Running `swap(a, b)` will swap the values of a and b.
    - This can be used to make the copy assignment operator (albeit slightly slower than the previous approach).
    - For this approach to work the destructor and copy-assignment operators must both exist.

```C++
struct Node {
	...
	// copy assignment operator (no copy-swap)
	Node& operator=(const Node& other) {
		// checks for self-assignment
		if (this == &other) { return *this; }
		// creates a temp node that stores next
		Node *temp = other.next ? new Node(*other.next) : nullptr;
		// clears the data of this to avoid memory leaks
		delete next;
		// assignment to this
		data = other.data;
		next = temp;
		// return for recursion
		return *this;
	}

	// copy assignment operator (with copy-swap)
	void swap(Node& other) { // new swap
	std::swap(data, other.data); // utility based swap
	std::swap(next, other.next);

	Node &operator=(const Node& other) {
		Node temp{other};
		swap(temp);
		return *this;
	}
	...
};
```

```C++
// example of the difference between copy constructor and assignment operator
Node n1 = new Node(50, nullptr);
Node n2 = new Node(60, new Node (80, nullptr)
Student n3 = n1; // copy constructor
n3 = n2; // copy assignment operator
```

Note that if `next` does not exist and the object is being called recursively for a deep copy, then a [[Ternary Operator]] needs to be used to account for this case.

```C++
struct Node {
	int data;
	Node *next;
	Node(const Node& other) : data{other.data}, next{other.next ? new Node(*other.next) : nullptr} {}
};
```

## Moving Objects

Moving objects are necessary since all arguments and values are defined when a function is called. This leads to wasted resources if only a select few of the resources are used. Moving objects makes this more efficient by reusing the originals instead of creating new values each time the function is called.

```C++
Node oddsOrEvens() {
	Node o{1, new Node {3, ... , new Node {99, nullptr}}};
	Node e{2, new Node {4, ... , new Node {100, nullptr}}};
	char c;
	cin >> c;
	return (c == 'o') ? o : e; // only one of o and e are actually useful
}

Node l = oddsOrEvens(); // return by value
```

[[References|R-Value Reverence]] is used over references for move operations since they bind to temporary values. This is important since variables that would otherwise be dangerous to steal (i.e. pointers) can be safely moved from it as its about to be destroyed anyways. This is also how move constructors are identified by the compiler.

There are 2 methods provided when moving objects in classes

The **Move Constructor** allow the fields of one object to be transferred to another object.

- To avoid double deletion errors when `other` is deleted, assign the `next` to `nullptr`

```C++
struct Node {
	...
	Node(Node&& other)
		: data{other.data}, next{other.next} { other.next = nullptr; }
	...
};
```

The **Move Assignment Operator** transfers the ownership of initialized objects to a variable that has already been initialized

```C++
struct Node {
	...
	// move assignment operator (no swap)
	Node &operator=(Node&& other) {
		if (this == &other) { return *this; }

		delete next;

		// transfer ownership
		data = other.data;
		next = other.next;

		// clear other
		other.next = nullptr;
		return *this;
	}

	// move assignment operator (with swap)
	Node& operator=(Node&& other) {
		if (this == &other) { return *this; }
		swap(other);
		return *this;
	}
	...
};
```

## Explicit and Implicit Constructors

**Implicit Constructors** are cases where a variable is converted from one type to another without explicit mentioning of the conversion

```C++
struct Node {
	...
	Node (int data, *next=nullptr): data{data}, next{next}{}
	...
}

f(Node{4}); // makes sense since f takes a Node
f(4); // also works, but does not make sense

string s = "Hello"; // example of implicit conversion, since a `const char*` type is converted automatically to a `std::string`
```

Implicit conversions are dangerous since the compiler does not provide a warning when an error like this occurs. To avoid this, use the keyword `explicit` to indicate that the argument types must match.

```C++
struct Node {
	int data;
	Node *next;
	explicit Node (int data, Node *next=nullptr): data{data}, next{next} {} // explicit constructor
};

Node n{4}; // works
Node m = 4; // does not compile

void f(Node n) {
	// function with n
}

f(Node{4}); // works
f(4) // won't compile

```

## Move/Copy Elision

**Elision** allows the object creating process to skip the copy and move constructors from the compiler. This happens because the function directly writes into the space defined by its caller.

```C++
Vec makeAVec() {
	return Vec{ 0, 0 }; // invokes the basic Vec constructor
}
Vec v  = makeAVec(); // just the basic constructor. Not copy nor move constructor.
```

# Features of Objects

## Member Operators

Any function that is defined as a class method automatically takes `this` as its first argument. Any functions that do not involve `this` can be written as standalone functions instead.

- The operators `=` `[]` `()` `<type>` `->` must be methods

```C++
// I/O operators need to be provided as standalone functions, since they are not always contained on the leftside of the operator.
struct Vec {
	int x;
	int y;
	public:
		...
		vec(int a, int b): x{a}, y{b} {}
		ostream &operator<<(ostream &out) {
			out << x << ' ' << y;
			return out;
		}
		...
};
```

- This can lead to some interesting interactions

```C++
// code has to be written this was since `this` is the first argument.
(b << ((a << cout) << spacer)) << endl;
```

## Arrays of Objects

To get an array of objects, ensure that either:

- There is a default constructor.
- If the array is stack allocated, provide values during initialization. This has limited use cases however.
- If the array is memory allocated, we can provide values after by manually initializing each index

```C++
struct Vec {
	int x, y;
	Vec (int x, int y): x {x}, y {y} {}
}

// both invalid since there is no default constructor
Vec *vp = new Vec[10];
Vec moreVecs[5];

// one potential solution is to provide a default constructor.
// However, since not all structures want a default constructor ():

// another solution is to define stack arrays
Vec moreVecs[3] = {{0, 0}, {1,1}, {2, 2}

// For heap arrays, create an array of pointers
Vec **vp = new Vec*[5];
vp [0] = new Vec {0, 0};
vp [1] = new Vec {1, 1};
// ...
for (int i = 0; i < 5; ++i) { delete vp[i]; }
delete[] vp;
```

## [[Constants]] in Objects

**`const` Objects** cannot have their fields changed nor have methods run on them after they are initialized. This is a flawed approach since an object without either of these properties is effectively useless.

To resolve this, we use the `const` keyword to indicate that a function will not modify an object's fields. This allows us to call the method on the object.

```C++
struct Student {
	int assns, mt, final;
	int calls = 0;
	float grade() const; // indicated const
};

float Student::grade() const {
	++calls;
	return assns * 0.4 + mt * 0.2 + final * 0.4; // valid even with const object
}
```

There are still fields that cannot be changed. Incrementing `calls` is invalid since declaring a function `const` means that none of the fields in the object can be changed..

To mutate these fields, declare them with the keyword `mutable`.

- Fields declared with `mutable` can be changed even if the object is `const`.
- `mutable` should be used sparingly to not contradict the use of `const`

`mutable` works by ignoring the **constness check**. There are 2 types of constness:

- **Physical Const-ness** is whether or not the actual bits of the function have changed or not. This is what the `const` keyword actually enforces
- **Logical Const-ness** is whether or not the object should be regarded as different.

This is the reason that heap-allocated memory can be changed, since only the fields of the object cannot be changed while the variables they point to can.

```C++
struct Student {
	int assns, mt, final;
	mutable int calls = 0; // now mutable field
	float grade() const; 
};

float Student::grade() const {
	++calls; // now works
	return assns * 0.4 + mt * 0.2 + final * 0.4; 
}
```

## Static Members

**Static Members** are associated with the class itself, not with any specific instance of the class. They are initialized with the keyword `static`

- The keyword `inline` allows static fields to be initialized directly in the interface instead of the implementation file
- Static member functions don't depend on the specific instance (therefore no `this`). Can only access other static fields and static member functions

```C++
struct Student {
	...
	int assns, mt, final;
	inline static int numInstances = 0;
	Student (int assns, int mt, int final) : assns{assns}, mt{mt}, final{final} {
		++numInstances;
	}
	static void howMany() { // can be called using Student::howMany();
		cout << numInstances << endl;
	}
	...
};

Student s{1, 2, 3};
Student r{1, 2, 3};
Student::howMany(); // outputs 2
```
