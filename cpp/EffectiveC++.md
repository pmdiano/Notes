### Item 4: Make sure that objects are initialized before they're used.

* Data members that are `const` or are references must be initialized; they can't be assigned.
* The order in which an object's data is initialized is always the same: base classes are initialized before derived class, and within a class, data members are initialized in the order in which they're **declared**.
* A *static object* is one that exists from the time it's constructed until the end of the program. Included are global objects, objects defined in namespace scope, objects declared `static` inside classes, objects declared `static` inside functions (which are non-local static objects), and objects declared `static` at file scope.
* The relative order of initialization of non-local static objects defined in different translation units is undefined. Instead, move each non-local static object into its own functin, where it's declared `static`. These functions return references to the objects they contain. Singleton pattern.

### Item 5: Know what functions C++ silently writes and calls.

* If you don't declare them yourself, compilers will declare their own versions of a copy constructor, a copy assignment operator, a destructor, and a default constructor if you declare no constructors. So this

```cpp
class Empty{};
```

is essentially this:

```cpp
class Empty {
public:
  Empty() {...}                               // default constructor
  Empty(const Empty& rhs) {...}               // copy constructor
  ~Empty() {...}                              // destructor
  Empty& operator=(const Empty& rhs) {...}    // copy assignment operator
};
```

* The generated destructor is non-virtual unless it's for a class inheriting from a base class that itself declares a virtual destructor.
* The compiler-generated copy constructor and copy assignment operator simply copy each non-static data member of the source object over to the target object.
* Compilers reject implicit copy assignment operators in derived classes that inherit from base classes declaring the copy assignment operators `private`.
* Following code won't compile, because C++ does not provide a way to make a reference refer to a different object:

```cpp
#include <string>
using namespace std;

template<class T>
class NamedObject {
public:
  NamedObject(string& name, const T& value)
    : nameValue(name)
    , objectValue(value) {
  }

private:
  string& nameValue;
  const T objectValue;
};

int main() {
  string newDog("Persephone");
  string oldDog("Satch");
  NamedObject<int> p(newDog, 2);
  NamedObject<int> s(oldDog, 36);

  p = s; // boom
  return 0;
}
```
