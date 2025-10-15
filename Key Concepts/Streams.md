# Reading from Input/Output

There are 3 types of streams: `stdout`, `stdin`, and `stderr`.

```C++
cout << x; // outputs the content of 'x' to the `stdout`
cin << x; // reads the content of the `stdin` stream to `x` as the type of `x`
cerr << x; // outputs the content of 'x' to the 'stderr'
```

There are 2 ways to read from these streams: input and file stream. These are represented by abstracted interfaces

- `istream` and `ostream` read/write from input/output
- `ifstream` and `ofstream` read/write from files

## Notes

- The operator points in the direction of information flow (i.e. `cerr << x)
- `endl` indicates the end of a line.
    - It is used over `\n` since it forces everything in the **output buffer** to be displayed on the screen immediately if it hasn't already. This has many applications (i.e. catching bugs, real-time input responses)
    - A `cout` or `cerr` without an `endl` will add items to the buffer system until they are flushed out.
- `cin` contains a **fail bit** (`cin.fail()`) and an **EOF bit** (`cin.eof()`).
    - The fail bit is set to `true` when the read fails due to either an incorrect type or out of bounds
    - The EOF bit is set to `true` when the EOF is reached. Note that this will also trigger the fail bit.
    - Once the fail bit has been called, it will remain that way until the program has finished running. It must be reset by manually executing the command `cin.clear()`.
    - Another command, `cin.ignore();` discards the current character and goes to the next position.

## Examples and Extra Cases

```C++
// Reading into 2 variables at once will read one input, then another
import <iostream>;
using namespace std;
int main() {
	int x, y;
	cin >> x >> y;
	cout << x << y << endl;
}

// stdin -> "a b"
// stdout -> "ab\n"
```

```C++
// Reading and printing integers while skipping invalid input
import <iostream>;
using namespace std;
int main() {
	int x;
	while (true) {
		if (cin >> x) { cout << x << endl; }
		else if (cin.eof()) { break; }
		else {
			cin.clear();
			cin.ignore();
		}
	}
}
```

# Reading from Files

In **C**, accessing a file looks like this:

```C
#include <stdio.h>
int main() {
	char s[256]; // Assumes that no word is longer than 256 characters
	FILE *f = fopen("file.txt", "r"); // Reading
	while (true) {
		fscanf(f, "%255s", s);
		if (feof(f)) break;
		print("$s\n", s);
	}
	fclose(f); // Done with file, close
}
```

In **C++** accessing a file looks like this:

- `ifstream` is the type of the file, `f` is the variable that stores the input file, and `file.txt` is the name of the file that was inputted
    - This syntax works for all C++ variables (i.e. `int i{5}` is equivalent to `int i = 5;`)
- `f` does not need to be closed. It is automatically popped off the stack and uninitialized when the function is returned.

```C++
import <iostream>;
import <fstream>;
import <string>;
using namespace std;

int main() {
	ifstream f {"file.txt"};
	string s;
	while (f >> s) {
		cout << s << endl;
	}
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
