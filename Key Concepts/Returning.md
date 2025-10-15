There are 3 ways to return values:

- **Return by Value** directly returns a variable. This should be used the most often.

```C++
Node getMeNode(int x) {
	Node n{x, nullptr};
	return n;
}
```

- **Return by Pointer** returns a pointer to a variable (note that the variable must not be destroyed when the function is popped off the stack or it will return a **dangling pointer**). This can be achieved by either having the variable exist beforehand or allocating memory to it

```C++
// invalid
Node* getMeNode(int x) {
	Node p{x, nullptr};
	return &n;
}

// works
Node* getMeNode(int x) {
	Node *p = new Node{x, nullptr}
	return p;
}
```

- **Return by Reference** returns a variable reference. This is rare and can only be used to return non-local values (i.e. `cin`) since local variables will result in a **dangling reference**.

```C++
// invalid
Node& getMeANodeRef(int x) {
	Node n{x, nullptr};
	return n;
}
```
