## OOP from 3 perspectives
* *Programmers Perspective* - How to structure programs to lower the risk of bugs. 
* *Compiler's Perspective* - What the code/constructions actually mean, and what a compiler must do to support them. 
* *Designer's Perspective* -   How the tools that OOP provides can be used to build systems

**Overloading** is when the same symbol/function is used for multiple implementations (i.e. `>>` in [[Input and Output]]).
* They must differ in the number or type of arguments.
In **C**, functions need to be delegated for each datatype
```C
int negInt(int n) {return -n;}
bool negBool(bool b) {return !b;}
```
