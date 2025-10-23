**Default Function Arguments (DFA)** are arguments that are automatically substituted if one is not provided

- DFA's must be placed last in the argument list.

* The missing argument is supplied by the **caller**, not the function. A function fetches the parameters by reading them off the stack from where they are expected to be.
    - Therefore, the the function has no way of knowing if an argument is missing, and will interpret whatever memory in that location as the argument rather than the default parameter.
    - For this reason, default arguments go into the **interface file**, rather than the implementation

```C++
// the default is "file.txt" if no parameter is passed into printWordsInFile
void printWordsInFile(string name="file.txt") {
	ifstream file {name}
	for (string s; file >> s;) cout << s << endl;
}
```
