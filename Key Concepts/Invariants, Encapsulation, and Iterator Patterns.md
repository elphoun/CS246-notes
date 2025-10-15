# Invariants and Encapsulation

**Invariants** are properties of a data structure that are always assumed to be true.
**Encapsulation** provides a solution to users violating invariants. C++ uses `public` and `private` to ensure that underlying data is not interacted with (i.e. **black box**). This abstracts the information and makes it hard to mess around with.

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
