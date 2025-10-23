# Invariants and Encapsulation

**Invariants** are properties of a data structure that are always assumed to be true.
**Encapsulation** provides a solution to users violating invariants. C++ uses `public` and `private` to ensure that underlying data is not interacted with (i.e. **black box**). This abstracts the information and makes it hard to mess around with.

- `public` fields/methods can be accessed anywhere
- `private` fields/methods can only be called within other class methods.

```C++
struct Vec {
	private: // only accessible from inside Vec
		int x, y;
	public: // accessible anywhere
		vec(int x, int y);
		vec operator+(const vec& other) const;
};
```

This is why the `class` keyword is preferred over `struct` when creating classes. The only difference between the 2 is that all methods and fields are private by default in `class` and public by default in `struct`. Since private is preferred over public, `class` is preferred over `struct`

```C++
struct Node {
	int data;
	Node *next;
	...
	~Node() { delete next; }
}
```

## Example with Linked List

```C++
// list.cc
export module list;
export class List {
	struct Node;
	Node *theList = nullptr;
	public:
		void addToFront(int n);
		int &ith(int i);
		~List();
		...
}

// list-impl.cc
struct List::Node {
	int data;
	Node *next;
	...
	~Node() {delete next;}
}

void List::addToFront(int n) {
	theList = new Node{n, theList}
}

int &List::ith(int i) {
	Node *cur = theList;
	for (int n = 0; n < i; ++n) { cur = cur -> next};
	return cur -> data;
}
```

# Iterator Patterns

**Iterator Patterns** are a design pattern that maintain encapsulation with fast iteration. Without iterator patterns, looping through the list happens in $O(n^2)$ time.

- In linked lists, this guarantees that `next` is either a `ListNode` or `nullptr` while also not exposing the actual pointers

```C++
// Class Implementation
class List {
	struct Node;

	Node *head = nullptr;

	class Iterator {
		Node *cur;
		public:
			Iterator(Node *cur): cur{cur} {}
			Iterator& operator++() {
				cur = cur->next;
				return *this;
			}
			bool operator!=(const Iterator& other) const {
				return cur != other.cur;
			}
			int& operator*() const {
				return cur->data;
			}
	};

	Iterator begin() const {
		return Iterator{head};
	}
	Iterator end() const {
		return Iterator{nullptr};
	}
	// rest of methods from previous example
};

// in main.cc
int main() {
	List l;
	l.addToFront(1);
	l.addToFront(2);
	l.addToFront(3);
	for (List::Iterator it=l.begin; it!=l.end(); ++it) {
		cout << *it << "";
	} // runs in O(n) time
}
```

Classes with `begin` and `end` methods return some iterator type and either a `++`, `!=`, or `*` operator can use a **range-based for loop**

```C++
// n is a copy of l (ListNode)
for (int n : l) {
	cout << n << endl;
}

// references the data itself and modifies it
for (int &n : l) {
	++n;
}
```

# Friends

A flaw with this Iterator is the statement `auto it = List::Iterator{nullptr};`. It creates a valid iterator with `nullptr` that was not made by either `begin` nor `end`. This breaks encapsulation since only `List` should control how its iterators are made.

The **Friend Class** solves this issue by giving `List` special access to `Iterator`. Use the keyword `friend` to declare a class to be a friend class.

```C++
class Iterator {
	Node *cur;
	Iterator(Node *cur): cur{cur} {}

	public:
		friend class List; // List can access private fields in Iterator

		Iterator& operator++() {
			cur = cur->next;
			return *this;
		}
		bool operator!=(const Iterator& other) const {
			return cur != other.cur;
		}
		int& operator*() const {
			return cur->data;
		}
};
```

Functions can also be part of the friend group.

```C++
class Vec {
	int x, y;
	public:
		friend ostream& operator<<(ostream& out, const Vec& v);
};

ostream& operator<<(ostream& out, const Vec& v) {
    return out << "(" << v.x << ", " << v.y << ")"; // can still access x and y since its declared as a friend
}
```

# Accessors and Mutators

**Accessors and Mutators** provide controlled access to private variables so that variables can remain private and be modified/obtained safely.

```C++
class Vec {
	int x, y;

	public:
		int getX() const { return x; }
		void setX(int a) { x = a; }
		int getY() const { return y; }
		void setY(int b) { y = b; }
};
```
