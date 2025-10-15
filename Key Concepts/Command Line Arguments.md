**Command Line Arguments** are arguments that are provided at runtime. They are typically used to customize a program's behavior
```
./program abc 123
```

CLA can be accessed through the main function:
* `argc` represents the number of arguments (+ the program name)
* `argv` represents the array of strings corresponding to each argument (separated by whitespace)
```C++
int main(int argc, char* argv[]);
```

All CLA's are C-style strings in `argv`, To make them usable in C++, they need to be converted to C++ strings or some other datatype
```C++
import <iostream>;
import <string>;
using namespace std;

int main(int argc, char* argv[]) {
	for (int i = 1; i < argc; ++i) {
		string arg = argv[i];
		// ...
	}
}

// sum of all integers on command line
int main(int argc, char* argv[]) {
	int total = 0;
	for (int i = 1; i < argc; ++i) {
		string arg = argv[i];
		int n;
		if (istringstream iss{arg}; iss >> n) { total += n; }
	}
	cout << total << endl;
}
```