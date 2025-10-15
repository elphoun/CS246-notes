A **Ternary Operator** follows the syntax `<bool> ? <return_val> : <other_return_val>`

```C++
cin >> x;
string a;

// original approach with if/else
if (x == 0) { a = "Zero"; }
else { a = "Nonzero"; }

// simplification with ternary operator
a = x == 0 ? "Zero" ? "Nonzero";
```

