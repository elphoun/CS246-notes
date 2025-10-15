**Classes** are [[Structures]] with functions inside them.

- `s` is an object (particular instance of a class)
- `::` is the **scope resolution operator**, and defines a function in the context of a class
- `<class_name>::<func_name>` is a member function (method)

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

# Big 5

Constructors create objects. Their main benefit is that they allow field-by-field customization and therefore have more flexibility.

There are 2 types of fields:

- **Primative Fields:** Compiler provided fields that are left uninitialized (i.e. `bool`, `int`)
- **Object Fields:** Calls the object's default constructor (i.e. `class`, `struct`)

The steps for object creation are:

1. Enough space is allocated.
2. Fields are initialized and the default constructor is called
3. Constructor body runs.

This means that the fields are initialized BEFORE the default constructor is called. Hence, having no default constructor and initializing a class with no arguments will lead to a **compilation error**

```C++
struct Basis {
	vec v1, v2;
}

Basis b; // error since v1 and v2 do not have a default constructor
```

## Move/Copy Elision

**Elision** allows the object creating process to skip the copy and move constructors from the compiler. This happens because the function directly writes into the space defined by its caller.

```C++
Vec makeAVec() {
	return Vec{ 0, 0 }; // invokes the basic Vec constructor
}
Vec v  = makeAVec(); // just the basic constructor. Not copy nor move constructor.
```

## Member Initialization List (MIL)

All classes come with the following. If one of these are written, then the rest should also probably be written.

- **Default Constructor**
- **Copy Constructor**
- **Copy Assignment Operator**
- **Destructor**
- **Move Constructor**
- **Move Assignment Operator**

### Default Constructor

To resolve the issue with the default constructor, we will use the **Member Initialization List (MIL)**. The MIL provides default values to to initialize the fields in step 2 instead of using the default constructor in step 3.

- The MIL follows the syntax `<field_name>{<properties>} ...`.
- The MIL is only provided in the interface file

```C++
struct Basis {
	vec v1, v2;
	Basis(): v1{0, 1}, v2{1, 0} {
		// Constructor Body
	}
}
```

### Custom Constructor

A custom constructor is also typically provided for the user to customize their object. There are 2 ways to define this

- The order of the fields does not matter
- The MIL is more efficient than setting values in the body since the overwrite step is saved
- The MIL MUST be used for object fields without default constructors, const fields, and reference fields.

```C++
// Option 1: Provding Default Values in the Default Constructor
struct Basis {
	vec v1, v2;
	Basis(): v1{0, 1}, v2{1, 0} {}
	Basis(const vec& v1, const vec& v2): v1{v1}, v2{v2} {}
};

// Option 2: Providing Default Values in Declaration
struct Basis {
	vec v1{1, 0}, v2{0, 1};
	Basis() {};
	Basis (const vec& v1, const vec&v2): v1{v1}, v2{v2} {}
}
```

### Copying Objects

There are 2 methods provided when copying objects in each class, the **copy constructor** and the **copy assignment constructor**

```C++
struct Student {
	int assns, mt, final;
	// copy constructor
	Student(const Student& other): assns{other.assns}, mt{other.mt}, final{other.final} {}
};

Student s{60, 70, 80};
Student r{s}; // copy constructor
Student t = s;
```

The reason these constructors are mandatory is because adding any constructor will overwrite the compiler-provided default constructors

- Copy constructor makes a copy of , but sets `next` to the pointer of `n`
- This means that any changes made to either `n`, `p`, or `q` will affect the same linked list in memory

```C++
// Linked List Node
struct Node {
	int data;
	Node *next;
}

Node *n = new Node{1, new Node{2, new Node {3, nullptr}}};
Node p = *n;
Node *q = new Node{*n}; // only copies the address of n.
```

Note that if `next` does not exist and the object is being called recursively for a deep copy, then a [[Ternary Operator]] needs to be used to account for this case.

```C++
struct Node {
	int data;
	Node *next;
	Node(const Node& other) : data{other.data}, next{other.next ? new Node(*other.next) : nullptr} {}
};
```

**The Copy Assignment Operator** runs when a value is assignment to another of the same type.

```C++
struct Node {
	Node& operator=(const Node& other) {
		if (this == &other) { return *this; } // exit if self assignment occurs
		// if memory cannot be allocated, next is deleted. We avoid this by assigning nullptr if next exists.
		Node *temp = other.next ? new Node(*other.next) : nullptr;
		delete next; // delete current objects to avoid memory leaks
		data = other.data;
		next = temp;
		return *this; // for chaining
	}
};

Node n1 = new Node(50, nullptr);
Node n2 = new Node(60, new Node (80, nullptr)

Student n3 = n1; // copy constructor
n3 = n2; // copy assignment operator
```

#### Copy Swap Idiom

In the library `utility` there is a function called `swap`. Running `swap(a, b)` will swap the values of a and b. This can be used to make the copy assignment operator (albeit slightly slower than the previous approach)

```C++
struct Node {
	void swap(Node& other) { // new swap
		std::swap(data, other.data); // utility based swap
		std::swap(next, other.next);
	}

	Node &operator=(const Node& other) {
		Node temp{other};
		swap(temp);
		return *this;
	} // note that the destructor and the copy constructor must be defined or there will be memory leaks.
};
```

### Moving Objects

**Move constructors** allow the fields of one object to be transferred to another object.

Move constructors are necessary since all values are defined when a function is called, leading to wasted resources if only a select few are used. They solve this issue by reusing the originals instead of creating new ones each time the function is called

```C++
Node oddsOrEvents() {
	Node o{1, new Node {3, ... , new Node {99, nullptr}}};
	Node e{2, new Node {4, ... , new Node {100, nullptr}}};
	char c;
	cin >> c;
	return (c == 'o') ? o : e; // only one of o and e are actually useful
}

Node l = oddsOrEvens(); // return by value
```

The **R-value reverence `Node &&`** allows the compiler to reserve a temporary memory address to put the value in

```C++
struct Node {
	// move constructor
	Node (Node&& other): data{other.data}, next{other.next} {
		// removes the other's access to the remaining nodes so that the rest of the list does not get destroyed
		other.next = nullptr;
	}
}
```

The **Move Assignment Operator** is used when reassigning the value of a variable that has already been initialized

```C++
struct Node {
	Node& operator=(Node&& other) {
		swap(other);
		return *this;
	}
};
```

### Explicit and Implicit Constructors

**Implicit Constructors** are cases where a variable is converted from one type to another without explicit mentioning of the conversion

```C++
void f(Node n) {
	// function with n
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

### Destructors

**Destructors** are called by default when a:

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
	... // fields and constructors
	~Node() { delete next; }
}
```

# Features of Objects

## Member Operators

Any function that is defined as a class method automatically takes `this` as its first argument. Any functions that do not involve `this` can be written as standalone functions instead.

- The operators `=` `[]` `()` `<type>` `->` must be methods

```C++
// I/O operators need to be provided as standalone functions, since they are not always contained on the leftside of the operator.
struct Vec {
	...
	ostream &operator<<(ostream &out) {
		return out << x << ' ' << y;
	}
}
```

## Arrays of Objects

To get an array of objects, ensure:

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
// However, this is flawed since not all structures want a default constructor ()

// another soltuion is to define stack arrays
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

**Constant Objects** cannot have their fields changed nor have methods run on them after they are initialized. This is a flawed approach since an object without either of these properties is effectively useless.

To resolve this, we use the `const` keyword to indicate that a function will not modify an object's fields. This allows us to call the method on the object.

```C++
struct Student {
	int assns, mt, final;
	float grade() const; // indicated const
};

float Student::grade() const { // valid with const even with const object
	return assns * 0.4 + mt * 0.2 + final * 0.4;
}
```

There are still fields that cannot be changed. For instance, if `calls` was a field that was incremented each time in grade, then the function would be invalid since it mutates something related to the object.

To mutate these fields, declare them with `mutable`.

- Fields declared with `mutable` can be changed even if the object is `const`.
- `mutable` should be used sparingly to not contradict the use of `const`

Mutable works by ignoring the **constness check**. There are 2 types of constness:

- **Physical Const-ness** is whether or not the actual bits of the function have changed or not
- **Logical Const-ness** is whether or not the object should be regarded as different.

```C++
// invalid case since it affects the physical constness (implying logical constness change) of Students
struct Student {
	int numMethodCalls = 0;
	float grade() const {
		++numMethodCalls;
		return numMethodCalls;
	}
}

// valid with mutable since it skips the check
struct Student {
	...
	mutable int numMethodCalls = 0;
	float grade() const {
		++numMethodCalls;
		return numMethodCalls;
	}
}
```

## Static Members

**Static Members** are associated with the class itself, not with any specific instance of the class.

- The keyword `inline` makes a variable a static member
- Static member functions don't depend on the specific instance (therefore no `this`). Can only access other static fields and static member functions

```C++
struct Student {
	...
	inline static int numInstances = 0;
	Student ( ___ ) : ___ {
		++numInstances;
	}
	static void howMany() { // can be called using Student::howMany();
		cout << numInstances << endl;
	}
	...
};
```
