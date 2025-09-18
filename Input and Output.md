## I/O Streams
* `cout` and `cerr` for printing to `stdout` and `stderr` respectively
* `cin` for reading from `stdin`. `cin` ignores whitespace by default. If there is an error, then the function call `cin.fail()` will be true. At the end of the file, `cin.fail()` and `cin.eof()` are both true. 
* Everything that comes in from the input stream is a `string`
## I/O Operators
* `<<` means "put to" output
* `>>` means "get from" output
* The operator points in the direction of information flow (i.e. `cerr << x)

```C++
# Adds 2 Numbers
int main() {
	int x, y;
	cin >> x >> y;
	cout << x + y << endl;
}
```
```C++
# Read all int from stdin. Stop on bad
int main() {
	int i;
	while (true) {
		cin >> i;
		if (cin. fail()) { break; }
		cout << i  << endl;
	}
}
```

Another meaning of the `>>` operator is the **right bit shift operator** in both `C` and `C++`.
Let `a` and `b` are integers. `a >> b` shifts `a` to the right by `b` bits. 
```c++
21 = 10101 // Cutoff the last 3 digits to get 10
2 = 10
```
Therefore `21 >> 3 = 2`

---
```c++
// Read and print all ints until EOF
int main()  {
	int i;
	while (true):
		if (!(cin >> c)) { // Can fail for multiple reasons. This checks for the error
			if (cin.eof()) break; // Fails because its hit the end of file.
			cin.clear(); // Resets the stream's failure flags. It will not function otherwise
			cin.ignore(); // Ignores the offending character in the input stream
		} else {
			cout << i << endl; /// prnt
		}	
}
```

An **I/O manipulator** changes the stream into a mode that reads characters in a certain data type. This works with `cin` as well. 
```c++
cout << hex << 45 // Outputs 45 in hexidecimal
cout << 50 // Also Outputs in hexidecimal

/**
cout << dec | Decimal
cout <<  hex | Hexidecimal
**/
```
* When working with I/O manipulators, make sure to change them back to the original status 

---
Rather than `stdout` and `stdin`, you can use **files** as output and input. These all share the same functionality. 
```C++
std::ifstream // File Stream for Reading
std::ofstream // File Stream for Writing
```

In **C**, accessing a file looks like this:
```C
#include <stdio.h>
int main() {
	char s[256]; // Assumes that no word is longer than 256 characters
	FILE *f = fopen("file.txt", "r"); // Reading
	while (1) {
		fscanf(f, "%255s", s);
		if (feof(f)) break;
		print("$s\n", s);
	}
	fclose(f); // Done with file, close
}
```

In **C++** accessing a file looks like this:
```C++
import <iostream>;
import <fstream>;
import <string>;
using namespace std;

int main() {
	ifstream f {"file.txt"}; // Declares and initializes the ifstream opens the file.
	string s;
	while (f >> s) {
		cout << s << endl;
	}
	// The file f is a part of the function stack. When the function is popped off the stack the file is uninitialized and therefore closed. 
}
```

You can also read and write strings directly:
```C++
std::istringstream // Extract data from characters in a string
std::ostringstream // Sends data to a string as characters.
```

```C++
/* intToString converts an int into a string*/
import <sstream>;
string intToString(int n) {
	ostringstream oss; // Stream that writes to a string
	oss << n; // adds n to the oss
	return oss.str(); // extracts the string
}
```

```C++
/*stringToInt converts a string to an int*/
int stringToInt() {
	int n;
	while (true) {
		cout << "Enter a Number" << endl;
		string s;
		cin >> s;
		if (istringstream iss {s}; iss >> n) break; // If fail to read a number, then break
		cout << "I said, ";
	}	
	cout << "You Entered" << n << endl; // print "You Entered {n}"
}
```

```C++
/* Echo all int's, skip all non-int's */
int main() {
	string s;
	while (cin >> s) {
		int n;
		if (istringstream iss {s}; iss >> n) {
			cout << n << endl
		};
	}
}
```

To accept command line arguments in **C** or **C++**, always give them the following parameters:
```C++
int main(int argc , char *argv[]) {
	for (int i = 1; i < argc; ++i) {
		string arg = argv[i];
		...
	}
}
/*
argc is the number of cmd line arguments
argv is an array of C-style strings.
	argv[0] = program name 
	argv[1 - (n - 1)] = 1st argument
	argv[n] = null
*/
```

```C++ 
/* Print the sum of all numerical arguments on the cmd line */
int main (int argc, char *argv[]) {
	int sum = 0;
	for (int i = 1; i < argc; ++i) {
		string arg = argv[i]
		int n;
		if (istringstream iss {s}; iss > n) {
			sum += n
		}
	}
	cout << sum << endl;
}
```

Default Function Arguments. These must be placed last in the argument list.
* The missing argument is supplied by caller, not the function. A function fetches the parameters by reading them off the stack from where they are expected to be.
* Therefore, the the function has no way of knowing if an argument is missing, and will interpret whatever memory in that location as the argument rather than the default parameter. 
* For this reason, default arguments go into the **interface file**, rather than its implementation.
```C++
void printWordsInFile(string name="file.txt") { // the default is "file.txt" if no parameter is passed into printWordsInFile
	ifstream file {name}
	for (string s; file >> s;) cout << s << endl;
}
```
