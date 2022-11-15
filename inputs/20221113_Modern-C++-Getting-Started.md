# Modern C++: Getting Started

Some assumptions:

- Previous C++ basic programming knowledge.

---

#### Getting started with Makefile

Makefiles are a way to configure your build setup, powerful and simple to use.

A very simple example to compile a `./src/main.cpp` file with a cpp program into a `./build/main.o` binary file:

```Makefile
CXX=g++

SRC_DIR=./src
SRC=$(SRC_DIR)/main.cpp

OUT_DIR=./build
OUT=$(OUT_DIR)/main.o

build:
  mkdir -p $(OUT_DIR)
  $(CXX) $(SRC) -o $(OUT)

clean:
  rm -rf $(OUT_DIR)
```

You can define alias to values like, files, folders, programs just like this:

```Makefile
ALIAS=value
```

To define command alias, you can do like this:

```Makefile
command:
  echo "some custom command!"
```

To combine those you can do like this:

```Makefile
ALIAS="some custom command!"

command:
  echo $(ALIAS)
```

and this is the basic `Makefile` knowledge you need to get started.

---

#### Getting started with modern C++

In this post I'll talk about C++ different ways to pass parameters, classes and smart pointers,
show an simple example and then talk about those concepts.

A simple program to implement an inverse linked list:

```cpp
// main.cpp
#include <iostream>
#include <memory>

class Node {
public:
  int value;
  std::shared_ptr<Node> next;

  Node(int value, const std::shared_ptr<Node>& next)
    : value(value), next(next) {
    std::cout << "Creating: " << this->value << std::endl;
  }

  ~Node() {
    std::cout << "Destroying: " << this->value << std::endl;
  }
};

class LinkedList {
public:
  std::shared_ptr<Node> head = nullptr;

  void add_node(int value) {
    this->head = std::make_shared<Node>(value, this->head);
  }

  void print() {
    std::shared_ptr<Node> tmp = this->head;

    while (tmp != nullptr) {
      std::cout << tmp.get() << ": " << tmp->value << std::endl;
      tmp = tmp->next;
    }
  }
};

int main() {
  LinkedList linked_list;
  linked_list.add_node(1);
  linked_list.add_node(2);
  linked_list.add_node(3);
  linked_list.print();
  return 0;
}
```

The program output:

```shell
Creating: 1
Creating: 2
Creating: 3
0x55effa696330: 3
0x55effa696300: 2
0x55effa695ec0: 1
Destroying: 3
Destroying: 2
Destroying: 1
```

#### Explaning used concepts

##### Passing parameters:

**Passing by value:**

This type of parameter make a copy of the passed value to the function scope, do not change the passed variable.

```cpp
void func(T);
```

**Passing by reference (alias):**

This type pass a reference of the passed value to the function scope, so,
if you change the value, the passed variable you change too.

```cpp
void func(int&);

int a = 1;

func(a);
```

Also can be passed by pointer and the variable can be also changed, but you have to defer the pointer first:

```cpp
void func(int*);

int a = 1;

func(&a);

void func(int* value) {
  std::cout << *value << std::endl;
}
```

**Passing by constant reference:**

This type pass a reference of the passed value to the function scope, but you can change this value.

```cpp
void func(const int&);

int a = 1;

func(a);
```

##### Classes:

I like classes, do you like classes too?

Here is a simple example of a class in cpp, with a `constructor`, a `destructor` and `operator` overloading.

```cpp
// main.cpp
#include <iostream>

class Vec2 {
public:
  int x, y;

  Vec2(int x, int y) : x(x), y(y) {}
  ~Vec2() {}

  Vec2 operator+(const Vec2& vec) {
    return Vec2(this->x + vec.x, this->y + vec.y);
  }
};

int main() {
  Vec2 vec_a(1, 2);
  Vec2 vec_b(3, 4);

  Vec2 another_vec = vec_a + vec_b;

  std::cout << another_vec.x << ", " << another_vec.y << std::endl;

  return 0;
}
```

The program output:

```shell
4, 6
```

**Constructor:**

They have two ways to instanciate:

```cpp
MyClass instance; // on the stack
MyClass* instance = new MyClass; // on the heap
```

You can use the smart pointers constructors helpers to instanciate classes too, they will be located on the heap.

- The constructor is called when you try to create a new object from a class, it can be used for:
  - add values the properties;
  - make a copy of the object using a copy constructor.

**Destructor:**

- The destructor is called when:
  - the end of the scope is reached, if the object is located on the stack;
  - the `delete` operator is called, e.g: `delete instance`, if the object is located on the heap.

**Operator overload:**

- With overloading a operator you add new capabilities to a class, available operators to overload:
  - binary arithmetic: `+`, `-`, `*`, `/`, `%`;
  - unary arithmetic: `+`, `-`, `++`, `—`;
  - assignment: `=`, `+=`, `*=`, `/=`, `-=`, `%=`;
  - relational: `>`, `<`, `==`, `<=`, `>=`;
  - bit-wise: `&`, `|`, `<<`, `>>`, `~`, `^`;
  - logical: `&`, `||`, `!`;
  - subscript: `[]`;
  - function call: `()`;
  - de-referencing: `->`;
  - dynamic memory allocation and de-allocation: `new`, `delete`.

In classes you have "data" and "actions" in the same place, and also have different
type of access for that (`public`, `protected`, `private`), so you can make nice abstractions with it.

##### Smart pointers:

Smart pointers are basically auto managed pointers, wrapper's to raw pointers.
You can access they in the standard library by including `memory` header.

NOTE: if you can use reference instead of a pointer, use the reference.

**Shared pointer:**

```cpp
std::shared_ptr<T> my_ptr;
```

Constructor helper:

```cpp
std::shared_ptr<T> my_ptr = std::make_shared<T>();
```

The `std::shared_ptr` is a smart pointer that retains shared ownership of an object through a pointer.

- Several `shared_ptr` objects may own the same object. The object is destroyed and its memory deallocated when either of the following happens:
  - the last remaining `shared_ptr` owning the object is destroyed;
  - the last remaining `shared_ptr` owning the object is assigned another pointer via `operator=` or `reset()`.

The object is destroyed using delete-expression or a custom deleter that is supplied to `shared_ptr` during construction.

**Unique pointer:**

```cpp
std::unique_ptr<T> my_ptr;
```

Constructor helper:

```cpp
std::unique_ptr<T> my_ptr = std::make_shared<T>();
```

The `std::unique_ptr` is a smart pointer that owns and manages another object through a pointer and disposes of that object when the `unique_ptr` goes out of scope.

- The object is disposed of, using the associated deleter when either of the following happens:
  - the managing `unique_ptr` object is destroyed
  - the managing `unique_ptr` object is assigned another pointer via `operator=` or `reset()`.

**Weak pointer:**

```cpp
std::weak_ptr<T> my_ptr;
```

There's no constructor helper to weak pointers.

The weak pointer is a smart pointer that holds a non-owning ("weak") reference to an object that is managed by `std::shared_ptr`.
It must be converted to `std::shared_ptr` in order to access the referenced object.

Smart pointer descriptions source: [cppreference.com/w/cpp/memory](https://en.cppreference.com/w/cpp/memory).
To more info about those concepts, check it out the best C++ docs: [cppreference.com](https://en.cppreference.com/w/cpp).

- Next steps to keep learning:
  - template meta-programming;
  - using downloaded C++ libraries;
  - creating a C++ library.

---

**Book recomendations:**

- [Effective Modern C++ by Scott Meyers](https://www.oreilly.com/library/view/effective-modern-c/9781491908419/)
  - Just do not pick the PT-BR translated version.

**A fast way to start a C++ project:**

You can use my boilerplate: [CPP-Boilerplate](https://github.com/Raisess/cpp-boilerplate).

```shell
git clone https://github.com/Raisess/cpp-boilerplate my-cpp-project
```

Change `CXX` to your desired compiler, default is `g++`.

And that's all, see ya!

---

<figure class="text-start">
  <blockquote class="blockquote">
    <p>Your assumptions are your windows on the world. Scrub them off every once in a while, or the light won't come in.</p>
  </blockquote>
  <figcaption class="blockquote-footer">
    <a href="https://en.wikipedia.org/wiki/Isaac_Asimov" target="_blank">Isaac Asimov</a>
  </figcaption>
  <img alt="Isaac Asimov" width="230" height="320" src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/34/Isaac.Asimov01.jpg/220px-Isaac.Asimov01.jpg" />
</figure>
