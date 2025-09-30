## Defintion

**Structures** are defined by calling `struct`.

```C++
struct Node {
	int data;
	Node *next; // no need for the struct call at the beginning
}; // make sure to add the semicolon
```

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