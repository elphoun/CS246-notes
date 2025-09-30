Default Constructors
---
All classes come with a **default constructor** (constructs the default values).
```C++
Vec v; // default constructor
```

The default constructor goes away if you write any arguments into the constructor. 
```C++
struct Vec {
	int x, y;
	Vec (int x, int y) {
		this -> x = x;
		this -> y = y;
	}
};

Vec v; // Invalid now that the vector writing has been overwritten
Vec v { 1, 2 }; // Now valid. 
```

Now that we've removed the default constructor for `Vec`, defining another structure without arguments is an invalid call.  
```C++
struct Basis {
	Vec v1, v2;
};

Basis b; // invalid now.
```

Note that this call also doesn't work. The call to `Vec` is being made too late for `Basis` to set the fields 
```C++
struct Basis {
	Vec v1, v2;
	Basis () {
		v1 = Vec{ 1, 0 };
		v2 = Vec { 0, 1 };
	}
}
```

Object creation steps
1. Space is allocated
2. fields are constructed in declaration order (i.e. constructors run for fields that are objects). 
3. Constructor runs 

The **Member Initialization List (MIL)** allows initialization of the variables to happen during step 2 rather than step 3.
* The syntax `<field_name> {<parameters>}` allows fields to be initialized in step 2 rather than step 3. 
* Fields are initialized in order in which they are declared in the class, even if the MIL orders them differently. Make sure the declaration order matches. 
* MIL must be used for fields that are objects with no default, references, and constants. 
```C++
Student :: Student (int assns, int mt, int final): // note the colon. 
	assns { assns }, mt { mt }, final { final } // step 2
	{} // step 3
```

All fields can be initialized in this way:
```C++
struct Basis {
	Vec v1, v2;
	Basis (const Vec &v1, const Vec &v2): v1 {v1}, v2 {v2} 
	{}
}
```

The **Copy Constructor** is used to construct an object as a copy of another. All classes also come with a copy constructor.
```C++
struct Basis {
	Vec v1{1, 0}, v2{0, 1};
	Basis () {}
	Basis (const Vec &v1, const Vec &v2); v1 {v1}, v2 {v2}{} // The ending is the copy constructor
};
```

The **MIL** is much more efficient than setting fields in the body. 
```C++
struct Student {
	int assns, mt, final; // not objects
	string name;
	Student (int assns, string name) {
		this -> assns = assns;
		...
		this -> name = name;
	}
}
```

All Classes come with the following
* **Default Constructor**
* **Copy Constructor
* **Copy Assignment Operator**
* **Destructor**
* **Move Constructor**
* **Move Assignment Operator**

You can build your own copy constructor. This is primarily used when the built-in copy constructor does not do its job properly:
```C++
struct Student {
	int assns, mt, final;
	...
	Student (const student &other):
		assns {other.assns}.
		mt {other.mt},
		final {other.final},
		{}
}

// Usage Case
struct Node {
	int data;
	Node *next;
};

Node *n  = new Node  1, new Node {2, new Node {3, nullptr}}};
Node m = *n;
Node *p = new Node { *n };
```

Note that these are all **shallow copies** of the Node. To get a deep copy of the node, you need to write a copy constructor that recurses through the node tree.
```C++
struct Node {
	...
	Node (const Node &other): 
		data { other.data },
		// add a conditional that checks for a nullptr
		next { other.next ? new Node { *other.next} : nullptr} {} // note the dereference
};
```

The copy constructor is called
* When an object is initialized from another object of the same type
* when an object is passed by value
* When an object is returned by value ... for now. The truth is more nuanced. 