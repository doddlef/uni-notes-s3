### Imperative/logic/functional programming
- **Imperative** are based on **commands**, in the form of **instructions** and **statements**
	- commands are executed
	- have an effect, such as update state, ...
- **Logic** based on finding values that satisfy a set of **constraints**
	- may have multiple solutions or none
	- do not have an effect
- **functional** based on evaluating **expressions**
	- expressions are evaluated
	- do not have an effect

code has a **side effect** if, in addition to producing a value, it also modifies some state or has an observable interaction with calling functions or the outside world, also including:
- raise an exception
- write data to display, file, ...
- read data
- ...

in declarative languages, you create a new version of an object, but the old version would still be there. The price is that implementation has to work harder to _recover memory_ and to _ensure efficiency_, the benefit is no worry about what other code will be affected, and allows to keep previous versions. The **immutability** of data structures also makes _parallel_ programming easier.

could you guarantee?
- function does not update data structure?
- every piece of code that handles the structure has been updated to handle an added new field?
- function does not read or write global variables? no I/O?