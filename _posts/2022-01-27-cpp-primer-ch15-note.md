---
layout: post
title:  "C++ Primer CH 15 C++OOP 筆記"
date:   2022-01-27 10:20:31 +0800
author : "leolijung"
categories: 
---

### C++ primer chpater 15 C++ OOP

#### 15.2 Defining Base and Derived Classes

* **RULE **: Any non-static member function (§7.6, p. 300), other than a constructor, may be virtual.

* **derived本身含有 base的object**，了解繼承是怎麼運作的，例如轉型:

  The fact that a derived object contains subobjects for its base classes is key to how inheritance works.

* **使用base的constructor **，C++沒有super關鍵字:

   To use a different base-class constructor, we provide a constructor initializer using the name of the base class, followed (as usual) by a parenthesized list of arguments.

* **Preventing Inheritance**:使用final

```C++
class NoDerived final { /* */ }; // NoDerived can't be a base class
```

*  The **dynamic type** may not be known until run time. The **static type** of an expression is always known at compile time.
* The automatic derived-to-base conversion applies only for conversions to a **reference
  or pointer type**. 比如像下面這樣。

```C++
Bulk_quote bulk;
Quote *itemP = &bulk; // ok: dynamic type is Bulk_quote
Bulk_quote *bulkP = itemP; // error: can't convert base to derived
```

*  There is no such conversion from a derived-class type to the base class type，因為這個叫**copy/assign**

```C++
Bulk_quote bulk; // object of derived type
Quote item(bulk); // uses the Quote::Quote(const Quote&) constructor
item = bulk; // calls Quote::operator=(const Quote&)
```

#### 15.3 Virtual Functions

* **Debug技巧**，在derived的function後加上**override** ，這個bug超難de：The compiler will reject a program if a function marked override does not override an existing virtual function.

  ```c++
  struct B {
  	virtual void f1(int) const;
  	virtual void f2();
  	void f3();
  };
  struct D1 : B {
  	void f1(int) const override; // ok: f1 matches f1 in the base
  	void f2(int) override; // error: B has no f2(int) function
  	void f3() override; // error: f3 not virtual
  	void f4() override; // error: B doesn't have a function named f4
  ```
  
* 我的override的觀念要改了：**only a virtual function can be overridden** (似乎只有支援dynamic binding才叫override)

* final修飾member function：

```c++
void f1(int) const final; // subsequent classes can't override f1 (int)
```

* **預設引數**(Default Arguments)在**virtual function**中會有這樣的效果：Virtual Functions and Default Arguments : the derived function will be passed the default arguments defined for the base-class version of the function. 通通都是用base的**預設引數**。

#### 15.4 Abstract Base Classes

* double net_price(std::size_t) const = 0; 宣告pure virtual function

```C++
class Disc_quote : public Quote {
public:
	Disc_quote() = default;
	Disc_quote(const std::string& book, double price,
	std::size_t qty, double disc):
	Quote(book, price),
	quantity(qty), discount(disc) { }
	double net_price(std::size_t) const = 0;
protected:
	std::size_t quantity = 0; // purchase size for the discount to apply
	double discount = 0.0; // fractional discount to apply
};
```

#### 15.5. Access Control and Inheritance

* public, private, and protected Inheritance 對內部是沒有影響的

```c++
struct Pub_Derv : public Base {
// ok: derived classes can access protected members
int f() { return prot_mem; }
// error: private members are inaccessible to derived classes
char g() { return priv_mem; }
};
struct Priv_Derv : private Base {
// private derivation doesn't affect access in the derived class
int f1() const { return prot_mem; }
};
```

* Friends 沒有特權：Friends of the base have no special access to members of its derived classes, and
  friends of a derived class have no special access to the base class
* ```using``` declaration

```c++
class Derived : private Base { // note: private inheritance
public:
// maintain access levels for members related to the size of the object
using Base::size;
protected:
using Base::n;
};
```

#### 15.6. Class Scope under Inheritance

* 在C++ Name Collisions and Inheritance 不是override，只是蓋過外層scope同名的function而已
* **Best Practices**
  Aside from overriding inherited virtual functions, a derived class usually
  should not reuse names defined in its base class.
* 沒有overlaod base的function這種事 : As we’ve seen, functions declared in an inner scope do not overload functions declared in an outer scope.

```c++
struct Base {
int memfcn();
};
struct Derived : Base {
int memfcn(int); // hides memfcn in the base
};
Derived d; Base b;
b.memfcn(); // calls Base::memfcn
d.memfcn(10); // calls Derived::memfcn
d.memfcn(); // error: memfcn with no arguments is hidden
d.Base::memfcn(); // ok: calls Base::memfcn
```
#### 15.7 Constructors and copy control
* The destructor needs to be virtual to allow objects in the inheritance hierarchy to be dynamically allocated.
```cpp
class Quote {
public:
    // virtual destructor needed if a base pointer pointing to a derived object is deleted
    virtual ~Quote() = default; // dynamic binding for the destructor
};
```
* (13.1.6) We can prevent copies by defining the copy constructor and copy-assignment operator as deleted functions. 
```cpp
struct NoCopy {
NoCopy() = default; // use the synthesized default constructor
NoCopy(const NoCopy&) = delete; // no copy
NoCopy &operator=(const NoCopy&) = delete; // no assignment
~NoCopy() = default; // use the synthesized destructor
// other members
};
```
* If the default **constructor, copy constructor, copy-assignment** operator, or destructor in the base class **is deleted** or inaccessible (§15.5, p. 612), then **the corresponding member** in the derived class is defined as **deleted**, because the compiler can’t use the base-class member to construct, assign, or destroy the base-class part of the object.
* If the base class has an inaccessible or **deleted destructor**, then the synthesized default and copy constructors in the derived classes **are defined as deleted**, because there is no way to destroy the base part of the derived object.