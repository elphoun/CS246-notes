Strings in C are an array of `char` terminated by a null terminator (`\0`). There are a few disadvantages to this approach

- High risk with memory management
- The array needs to account for `\0`, and it cannot be overwritten

C++ solves this issue by introducing the `string` library.

```C++
import <iostream>;
import <string>;
using namespace std;
int main() {
	string s;
	cin >> s;
	cout << s << endl;
}
```

In `string`, many operations that make more intuitive sense can be used

```C++
s1 == s2, s1 != s2, s1 > s2, s1 < s2 // comparison of strings
s.length() // length of a string (runs in O(1) since the length is cached)
s1 + s2 // concatentation of 2 strings
s[1], s[2] // characters at an index.
```

# [[Input and Output]] with Strings

Rather than using `ifstream` and `ostream`, the `string` library

```C++
std::ostringstream; // prints a string (with formatting)
std::istringstream; // reads a string (with formatting)
```

Using `cin` to read strings will read the sequence of characters until it hits whitespace or EOL

```C++
// stdin -> "Hello"
cin >> x; // x = "Hello"

// stdin -> "Hell o"
cin >> x >> y; // x = "Hell", y = "o"
```

To read an entire line including whitespace, use `getline`

```C++
/* stdin ->
Hello Big Guy
Hello
*/
getline(cin, s); // s = "Hello Big Guy"
```

**Examples**
```C++
// Converting from an integer to a string using the function `str`
string intToString(int n) {
	ostringstream oss;
	oss << n;
	return oss.str();
}

// using istringstream to print all valid integer inputs
int main() {
	string s;
	while (cin >> s) {
		int i;
		if (istringstream iss{s}; iss >> i) { cout << i << endl; }
	}
}
```