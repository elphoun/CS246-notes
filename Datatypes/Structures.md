## Definition

**Structures** are defined by calling `struct`.

```C++
struct Node {
	int data;
	Node *next; // no need for the struct call at the beginning
}; // make sure to add the semicolon
```

# Explicit

Structure constructures that take in only one argument can have issues.

```C++
struct Node {
	...
	Node (int data, *next=nullptr): data{data}, next{next}{}
};

Node n {4}; // is ok
Node n = 4; // is also ok
string s = "Hello"; // s is a std::string, and "Hello" is a const char *. This assignment uses the same conversion
```

The problem with this is that integers are automatically converted to Nodes when passed into a function. This means that there could be issues while debugging.

```C++
void f(Node n);
f(4);
```

To resolve this, using the `explicit` conversion

```C++
struct Node {
	...
	explicit Node (int data, Node *next=nullptr): data{data}, next{next}{}
}
```

# Destructors

There are 2 ways to destroy a structure:

- **Stack Allocated:** goes out of scope
- **Heap Allocated:** is deleted manually

When a structure is destroyed:

1. Destructor body runs.
2. Field destructor is invoked in reverse order.
3. Space deallocated

A new destructor should be written when

```C++
// when np goes out of scope, the pointer is reclaimed since it is stack allocated, not heap allocated.
Node *np = new Node {1, new Node {2, new Node {3, nullptr}}}

delete np; // running delete does nothing since it only destroys structures.

// Instead, write a destructor to ensure that the whole list is freed
struct Node {
	...
	~Node() { deleete next; } // recursively calls delete. No need to check for nullptr.
}
```

# Copy Assignment Operator

```C++
Student s1 { 60, 70, 80 };
Student s2 = s1; // copy constructor
Student s3;
s3 = s1; // invalid since only one constructor can be run for each Student.
```

To perform deep copies, write your own!

```C++
// Make sure that the = operator works in the case of assignment
struct Node {
	...
	Node &operator = (const Node &other) {
		data = other.data;
		delete next;
		next = other.next ? new Node {*other.next} : nullptr; // wrong. note that you cannot copy and delete something at the same time.
		return *this;*
	}
}

// correct
struct Node {
	...
	Node &operator = (const Node &other) {
		if (this  == &other) { return &this; }
		data = other.data;
		delete next;
		next = other.next ? new Node { *other.next } : nullptr;
		return *this;
	}
}
```

self-assignment is a big deal. While `n = n;` is not likely, other types (i.e. `a[i] + a[j]);`) are

```C++
*p = *q;
a[i] = a[j];
```

```C++
Node &Node::operator = (const Node &other) {
	if (this  == &other) { return *this; }
	Node *tmp  = next;
	next = other.next ? new Node { *other.next } : nullptr;
	data = other.data;
	delete tmp;
	return *this. // if new fails the node is still valid;].
}
```

An alternative to swapping nodes is the `swap` function

```C++
import <utility>;
use namespace std;
struct Node {
	...
	void swap (Node &other) {
		swap(data, other.data); // this swap is from std
		swap(next, other.next); // this swap is from std
	}
}

Node &operator = (const Node &other) {
	Node tmp = other;
	swap(tmp);
	return *this;*
};


```

Note that n references a separate value in `oddsOrEvens`.

- The compiler creates a temp that holds the result, and assigns the reference to the temp. The copy constructor deep copies the data from the temporary to n.
- However. this is wasteful since the data in the temp is going to be discarded anyways. Instead, we should use the `&&` from the [[References]]

```C++
// Good, but inefficient
Node oddsOrEvens() {
	Node odds {1, new Node {3, new Node {5, nullptr}}};
	Node odds {2, new Node {4, new Node {6, nullptr}}};
	char c;
	cin >> c;
	if (c == '0') { return evens; }
	return odds;
}

// Correct
Node (Node &&other); // this is called a move constructor.
data {other.data};
next {other.next} {
	other.next  = nullptr.
}

Node n = oddsOrEvens(); // copy constructor from n to oddsOrEvents
```

```C++
// If no move constructor/move assignment are defined, then the copy versions will be used instead.
struct Node {
	...
	Node &operator = (Node &&other) {
		swap(data, other.data);
		swap(next, other.next);
		return *this; // temp will be destroyed here
	}
};
```
