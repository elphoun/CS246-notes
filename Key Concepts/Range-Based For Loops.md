Classes with `begin` and `end` methods return some iterator type and either a `++`, `!=`, or `*` operator can use a **range-based for loop**
```C++
// n is a copy of l (ListNode)
for (int n : l) {
	cout << n << endl;
}

for (int &n : l) {
	++n;
}
```