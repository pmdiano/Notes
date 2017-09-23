### Item 4: Make sure that objects are initialized before they're used.

* Data members that are `const` or are references must be initialized; they can't be assigned.
* The order in which an object's data is initialized is always the same: base classes are initialized before derived class, and within a class, data members are initialized in the order in which they're **declared**.
* A *static object* is one that exists from the time it's constructed until the end of the program. Included are global objects, objects defined in namespace scope, objects declared `static` inside classes, objects declared `static` inside functions (which are non-local static objects), and objects declared `static` at file scope.
* The relative order of initialization of non-local static objects defined in different translation units is undefined. Instead, move each non-local static object into its own functin, where it's declared `static`. These functions return references to the objects they contain. Singleton pattern.
