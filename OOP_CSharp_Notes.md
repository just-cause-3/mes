# OOPs (Object Oriented Programming) in C#

## Class

```csharp
class Car
{
    string color = "red";
}
```

Created a class `Car` with a variable `color`.

- When a variable is declared directly in a class, it is often referred to as a **field** (or attribute).
- It is not required, but is often a good practice to start with an uppercase first letter when naming classes.
- It is also common that the name of the C# file and the class match, as it makes code organized. However, this is not necessary, like in Java.

### C++ Equivalent

```cpp
class Car
{
    string color = "red";
};
```

- Same idea, almost identical syntax.
- C++ requires a semicolon `;` after the closing brace of a class definition — C# does not.
- C++ and C# classes both default members to `private`.

## Object

- An object is created from a class.
- To create an object of `Car`, specify the class name, followed by the object name, and use the keyword `new`.

```csharp
class Car
{
    string color = "red";
    static void Main(string[] args)
    {
        Car myObj = new Car();
        Console.WriteLine(myObj.color);
    }
}
```

- When `class Car { string color = "red"; }` was defined, it defined the class `Car`, i.e. the architecture of how the class should be — "draws painting of a house," but one cannot live in painting. → **No actual memory allocation.**

```csharp
class Car
{
    string color = "red";

    static void Main(string[] args)
    {
        Car myObj = new Car();
    }
}
```

- Then using `Car myObj = new Car();` assigns an instance, i.e. memory allocation, is done.
- `myObj` — variable that holds reference to that object.
- `Car` = the type defined.
- `new Car()` = the act of creating an actual object based on the blueprint.
- `myObj` = a reference/handle that lets you interact with the object.
- `string color = "red"` is the field defined. Every `Car` object will have a `color` field assigned to it, by default.

### C++ Equivalent

```cpp
#include <iostream>
using namespace std;

class Car
{
public:
    string color = "red";
};

int main()
{
    Car myObj;                  // stack allocation, no "new" needed
    cout << myObj.color;
}
```

- In C#, `new Car()` **always** allocates on the heap, and `myObj` is a reference to that object.
- In C++, `Car myObj;` allocates on the **stack** and `myObj` IS the actual object (value semantics, no reference).
- To replicate C#'s heap + reference behavior in C++:

```cpp
Car* myObj = new Car();   // heap allocation
cout << myObj->color;     // access members with ->
delete myObj;             // C++ requires manual cleanup (no garbage collector)
```

- This is the biggest conceptual difference: C# has automatic garbage collection, so `new` objects are cleaned up automatically. C++ `new` requires a matching `delete`, or you use smart pointers (`std::unique_ptr`, `std::shared_ptr`) to manage it automatically.

---

## Using Multiple Objects

```csharp
using System;
class Car
{
    public string color;
    public string brand;
    public string speed;

    public void Accelerate()
    {
        speed += 10;
    }

    public void Describe()
    {
        Console.WriteLine($"{brand} car, color: {color}, speed: {speed} km/h");
    }

    static void Main(string[] args)
    {
        Car car1 = new Car();
        car1.color = "blue";
        car1.brand = "Honda";
        car1.speed = 60;

        Car car2 = new Car();
        car2.color = "blue";
        car2.brand = "Tesla";
        car2.speed = 120;

        car1.Describe();
        car1.Describe();
        car1.Accelerate();

        Console.WriteLine("\nAfter car 2 accelerates:");
        car1.Describe();
        car2.Describe();
    }
}
```

**Output:**
```
Honda car, color: red, speed: 60 km/h
Tesla car, color: blue, speed: 120 km/h
After car 2 accelerates:
Honda car, color: red, speed: 70 km/h
Tesla car, color: blue, speed: 120 km/h
```

### C++ Equivalent

```cpp
#include <iostream>
using namespace std;

class Car
{
public:
    string color, brand;
    int speed;

    void accelerate()
    {
        speed += 10;
    }

    void describe()
    {
        cout << brand << " car, color: " << color << ", speed: " << speed << " km/h\n";
    }
};

int main()
{
    Car car1;
    car1.color = "blue";
    car1.brand = "Honda";
    car1.speed = 60;

    Car car2;
    car2.color = "blue";
    car2.brand = "Tesla";
    car2.speed = 120;

    car1.describe();
    car1.accelerate();

    cout << "\nAfter car 1 accelerates:\n";
    car1.describe();
    car2.describe();
}
```

- Identical structure to C#; main differences are `using namespace std;` and `cout <<` instead of `Console.WriteLine`.
- C++ uses `+=` for concatenation just like C#'s `+`, but `cout` chains values with `<<` rather than string interpolation (`$"..."`). C++20 does have `std::format` for something similar to `$"{brand} car..."`.
- All members declared `public:` since C++ defaults to `private`.

---

## Multiple Classes

**prog2.cs**
```csharp
class Car
{
    public string color = "red";
}
```

**prog.cs**
```csharp
class Program
{
    static void Main(string[] args)
    {
        Car myObj = new Car();
        Console.WriteLine(myObj.color);
    }
}
```

- One can create an object of a class and access it in another class.
- Often used for better organization of classes: one class has all the fields and methods, while the other class holds the `Main()` method (code to be executed).

### C++ Equivalent

Same concept, but C++ typically splits a class into a **header file** (`.h`) for the declaration and a **source file** (`.cpp`) for the definition, then `#include`s the header where needed.

**Car.h**
```cpp
#pragma once
#include <string>
using namespace std;

class Car
{
public:
    string color = "red";
};
```

**main.cpp**
```cpp
#include <iostream>
#include "Car.h"
using namespace std;

int main()
{
    Car myObj;
    cout << myObj.color;
}
```

- `#pragma once` (or `#ifndef` include guards) prevents the header from being included multiple times — C# doesn't need this since classes are referenced via namespaces/assemblies, not file includes.
- C++ has no built-in `Program` class requirement; `main()` is a free function, not a static method inside a class like C#'s `Main()`.

---

## Methods

- A method is a function that belongs to a class/object.
- It is defined inside a class and typically operates on that object's data (its fields/state).
- Called through an object using the dot notation.

```csharp
class Car
{
    public int speed;

    public void Accelerate()
    {
        speed += 10;
    }
}

Car car1 = new Car();
car1.Accelerate();
```

**Difference between a function and a method** is where the code "lives" and what it operates on.

A function is a standalone block of code that exists independently, i.e. it's not tied to any object or class. You can call it by its name and it can take inputs and return a value, and doesn't have automatic access to any object's data.

### Example of a method:
`public void`, `static void`

### For methods:
```
[access-modifier] [static?] [return type] MethodName(parameters)
```

- **access-modifier** (public, private, protected) — who can call it
- **static** (optional) — whether it belongs to the class or to the instances
- **Return type** (void, int, etc.)
- **Name + ()** — what makes it callable

### C++ Equivalent

```cpp
class Car
{
public:
    int speed;

    void accelerate()
    {
        speed += 10;
    }
};

Car car1;
car1.accelerate();
```

- Same structure: `[access-specifier] [return type] methodName(parameters)`.
- C++ access specifiers (`public:`, `private:`, `protected:`) apply to everything below them until the next specifier or end of class — unlike C#, where each member gets its own modifier individually.
- `static` works the same conceptually in both: a `static` method/field belongs to the class itself, not to any instance.
- A standalone **function** in C++ (declared outside any class, e.g. `int add(int a, int b) { return a + b; }`) is the same idea as C#'s description of a "function" — code with no automatic access to an object's fields.

---

## Class Members

```csharp
class MyClass
{
    string color = "red";
    int maxSpeed = 200;

    public void FullThrottle()
    {
        Console.WriteLine("The car is going as fast as it can!");
    }
}
```

Here members are 2 fields — `string color`, `int maxSpeed`, and one method — `public void FullThrottle()`.

### C++ Equivalent

```cpp
class MyClass
{
public:
    string color = "red";
    int maxSpeed = 200;

    void fullThrottle()
    {
        cout << "The car is going as fast as it can!";
    }
};
```

- Same idea — fields + methods bundled in a class.
- Naming convention differs: C# typically uses **PascalCase** for methods (`FullThrottle`), C++ commonly uses **camelCase** or **snake_case** (`fullThrottle` / `full_throttle`), though this is just convention, not a language rule.

---

## Constructors

- A constructor is a special method that is used to initialize objects.
- The advantage of a constructor is that it is called when an object of a class is created.
- It can be used to set initial values for fields.

```csharp
class Car
{
    public string model;

    public Car()
    {
        model = "Mustang";
    }

    static void Main(string[] args)
    {
        Car Ford = new Car();
        Console.WriteLine(Ford.model);
    }
}
```

**Note:** The constructor name must match the class name and it cannot have a return type (like `void` or `int`).

- The constructor is called when the object is created.
- All classes have constructors by default.

### C++ Equivalent

```cpp
#include <iostream>
using namespace std;

class Car
{
public:
    string model;

    Car()              // constructor: same name as class, no return type
    {
        model = "Mustang";
    }
};

int main()
{
    Car Ford;
    cout << Ford.model;
}
```

- Identical rule: constructor name = class name, no return type, in both languages.
- C++ also has a **default constructor** generated automatically if none is written, just like C#.
- C++ adds something C# doesn't have directly: a **destructor** (`~Car() { ... }`), called automatically when an object goes out of scope or is `delete`d — used for manual cleanup (e.g. freeing memory, closing files). C# instead relies on the garbage collector and optional `Dispose()`/`using` patterns.
- C++ also supports **member initializer lists**, a more efficient alternative to assigning in the body:
```cpp
Car() : model("Mustang") {}
```

---

## Properties

- Private access modifier means variables cannot be accessed from outside. Sometimes they need to be accessed, and this is done by properties.
- A property is like a combination of a variable and a method: a `get` and a `set` method.

### Example:

```csharp
class Person // class name
{
    private string name;
    public string Name
    {
        get { return name; }
        set { name = value; }
    }
}
```

### Explanation:
- `private string name;` — normal private field. Nothing outside class `Person` can access it.
- `public string Name { get { return name; } set { name = value; } }` — `Name` (capital N) is the public-facing interface to the private `name` field.
- **get**: what happens when someone reads the property. `get { return name; }`
- When code does `Person.Name`, this block runs and returns the value of `name`. It's like a method call disguised as a field access.
- **set**: what happens when someone writes to the property. `set { name = value; }`
- When code does `Person.Name = "John"`, this block runs. `value` is a special automatically provided keyword — it represents whatever was assigned on the right side of `=`. So, `value` here holds `"John"`.

### Full Example:

```csharp
using System;
class Person
{
    private string name;
    public string Name
    {
        get { return name; }
        set { name = value; }
    }
}

class Program
{
    static void Main(string[] args)
    {
        Person p = new Person();
        p.Name = "Alice";
        Console.WriteLine(p.Name);
    }
}
```

**Output:** `Alice`

### Syntax:
```csharp
private string name;
public string Name
{
    get { return name; }
    set { name = value; }
}
```

### Automatic way:
```csharp
public string Name { get; set; }
```

> **Encapsulation** is the OOP principle of bundling data (fields) and methods that operate on that data together inside a class, while restricting direct access to that data — controlling it instead through a well-defined interface (properties/methods), using private fields and methods to protect data.

### C++ Equivalent

C++ has **no built-in `get`/`set` property syntax** — encapsulation is done manually with getter and setter methods.

```cpp
#include <iostream>
using namespace std;

class Person
{
private:
    string name;

public:
    string getName()
    {
        return name;
    }

    void setName(string value)
    {
        name = value;
    }
};

int main()
{
    Person p;
    p.setName("Alice");
    cout << p.getName();
}
```

**Output:** `Alice`

- C#'s `get`/`set` blocks behave like hidden methods called via field-like syntax (`p.Name = "Alice"`). C++ has no such sugar — you call `p.setName("Alice")` and `p.getName()` explicitly as regular methods.
- C#'s automatic property (`public string Name { get; set; }`) has no direct C++ equivalent — C++ always needs an explicit private field plus explicit getter/setter methods.
- The **encapsulation principle itself is identical** in both: `private` hides the field, `public` methods control access to it.
- C++ does have **operator overloading** which can make objects "feel" like they have property-style access (e.g. overloading `operator[]` or `operator=`), but this is more advanced and not a direct equivalent.

---

## Inheritance

- **2 groups:**
  - **Derived Class (Child)** — class that inherits from another class.
  - **Base Class (Parent)** — the class being inherited from.

### Why and when to use inheritance?
- Useful for code reusability: reuse fields and methods of an existing class when you create a new class.

```csharp
class Vehicle // base class (parent)
{
    public string brand = "Ford"; // Vehicle field
    public void Honk()
    {
        Console.WriteLine("Tuut, tuut!");
    }
}

class Car : Vehicle // derived class (child)
{
    public string modelName = "Mustang"; // Car field
}

class Program
{
    static void Main(string[] args)
    {
        // Create a myCar object
        Car myCar = new Car();
        myCar.Honk();
        Console.WriteLine(myCar.brand + " " + myCar.modelName);
    }
}
```

> If you try to access a **sealed** class, C# will generate an error.

### C++ Equivalent

```cpp
#include <iostream>
using namespace std;

class Vehicle // base class (parent)
{
public:
    string brand = "Ford";

    void honk()
    {
        cout << "Tuut, tuut!\n";
    }
};

class Car : public Vehicle // derived class (child)
{
public:
    string modelName = "Mustang";
};

int main()
{
    Car myCar;
    myCar.honk();
    cout << myCar.brand << " " << myCar.modelName;
}
```

- Inheritance syntax: C# uses `class Car : Vehicle`, C++ uses `class Car : public Vehicle` — the `public` keyword specifies the **inheritance access level** (C++ also allows `protected` or `private` inheritance, which changes how base members are exposed to the derived class; C# doesn't have this option — all inheritance behaves like `public`).
- C#'s `sealed` keyword (prevents a class from being inherited) maps to C++'s `final` keyword: `class Car final { ... };`.
- C++ supports **multiple inheritance** directly (`class C : public A, public B`), which C# does not allow for classes (C# only allows multiple *interface* inheritance).

---

## Polymorphism

- Polymorphism means "many forms" and it occurs when we have many classes that are related to each other by inheritance.
- The idea is that the same method name can behave differently depending on which object calls it.

### 2 types of polymorphism in C#:
1. Method Overriding
2. Method Overloading

### 1) Method Overriding

```csharp
class Vehicle
{
    public virtual void Sound()
    {
        Console.WriteLine("Some generic vehicle sound");
    }
}

class Motorcycle : Vehicle // similar to Car
{
}

class Car : Vehicle
{
    public override void Sound()
    {
        Console.WriteLine("Brum");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Vehicle v1 = new Car();
        Vehicle v2 = new Motorcycle();

        v1.Sound();
        v2.Sound();
    }
}
```

- **virtual** in base class means "subclasses are allowed to provide their own version of this method".
- **override** in the subclass means "I'm providing that custom version".

`v1` and `v2` are both declared as type `Vehicle`, but calling `.Sound()` on each gives a different result, because C# looks at the actual object (`Car` or `Motorcycle`) at runtime, not the variable's declared type. This is called **runtime polymorphism**.

### C++ Equivalent (Method Overriding)

```cpp
#include <iostream>
using namespace std;

class Vehicle
{
public:
    virtual void sound()
    {
        cout << "Some generic vehicle sound\n";
    }
};

class Motorcycle : public Vehicle // similar to Car
{
};

class Car : public Vehicle
{
public:
    void sound() override
    {
        cout << "Brum\n";
    }
};

int main()
{
    Vehicle* v1 = new Car();
    Vehicle* v2 = new Motorcycle();

    v1->sound();
    v2->sound();

    delete v1;
    delete v2;
}
```

- `virtual` works the same way in both — marks a method as overridable.
- `override` is also a keyword in C++ (since C++11), used the same way as C# — it's optional but recommended, as it lets the compiler catch mistakes.
- **Key difference**: for runtime polymorphism to work in C++, you generally need **pointers or references** (`Vehicle*` or `Vehicle&`), not plain values like `Vehicle v1 = Car();`. If you write `Vehicle v1 = Car();` in C++, the object gets "sliced" down to just the `Vehicle` part, and `v1.sound()` would call the base version — this is a common C++ pitfall that doesn't exist in C# because C# reference types behave like pointers automatically.
- Without `virtual`, C++ resolves calls at **compile time** based on the declared type (no polymorphism) — C# methods are non-virtual by default too, but `override` requires `virtual` in the base regardless.

### Type 2: Method Overloading (compile-time polymorphism)

Same method name but different versions/for different inputs within the same class.

```csharp
class Calculator
{
    public int Add(int a, int b)
    {
        return a + b;
    }

    public double Add(double a, double b)
    {
        return a + b;
    }

    public int Add(int a, int b, int c)
    {
        return a + b + c;
    }
}

Calculator calc = new Calculator();
Console.WriteLine(calc.Add(2, 3));        // uses int, int
Console.WriteLine(calc.Add(2.5, 3.5));    // uses double, double
Console.WriteLine(calc.Add(1, 2, 3));     // uses int, int, int
```

Same method name `Add`, but C# picks the right version based on the number/type of arguments you pass.

### C++ Equivalent (Method Overloading)

```cpp
#include <iostream>
using namespace std;

class Calculator
{
public:
    int add(int a, int b)
    {
        return a + b;
    }

    double add(double a, double b)
    {
        return a + b;
    }

    int add(int a, int b, int c)
    {
        return a + b + c;
    }
};

int main()
{
    Calculator calc;
    cout << calc.add(2, 3) << "\n";       // uses int, int
    cout << calc.add(2.5, 3.5) << "\n";   // uses double, double
    cout << calc.add(1, 2, 3) << "\n";    // uses int, int, int
}
```

- Method overloading works **identically** in C++ and C# — same name, different parameter lists (different types and/or count), resolved at **compile time** based on the arguments passed.
- C++ additionally allows **default arguments** in function declarations (`int add(int a, int b = 0)`), which can sometimes substitute for overloads — C# also supports default parameter values similarly.

---

## Abstraction

- Data abstraction is the process of hiding certain details and showing only essential information to the user.
- **Abstract class** is a restricted class that cannot be used to create objects (to access it, it must be inherited from another class).
- **Abstract method**: can only be used in an abstract class, and it does not have a body. The body is provided by the derived class (inherited from).

An abstract class can have both abstract and regular methods.

```csharp
abstract class Animal
{
    public abstract void AnimalSound();

    public void Sleep()
    {
        Console.WriteLine("Zzz");
    }
}
```

```csharp
Animal myObj = new Animal(); // Error! Cannot create an instance of an abstract class
```

- To access an abstract class, it must be inherited from another class.

```csharp
abstract class Animal
{
    public abstract void AnimalSound();

    public void Sleep()
    {
        Console.WriteLine("Zzz");
    }
}

// Derived class (inherits from Animal)
class Pig : Animal
{
    public override void AnimalSound()
    {
        Console.WriteLine("The pig says: wee wee");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Pig myPig = new Pig();
        myPig.AnimalSound();
        myPig.Sleep();
    }
}
```

### Why use abstract?
To achieve security — hide certain details and only show the important details of an object.

### C++ Equivalent

C++ has no `abstract` keyword. Instead, a class becomes "abstract" automatically if it has at least one **pure virtual function** — declared with `= 0` and no body.

```cpp
#include <iostream>
using namespace std;

class Animal
{
public:
    virtual void animalSound() = 0;   // pure virtual = abstract method

    void sleep()
    {
        cout << "Zzz\n";
    }
};

// Animal myObj;  // Error! Cannot instantiate abstract class

class Pig : public Animal
{
public:
    void animalSound() override
    {
        cout << "The pig says: wee wee\n";
    }
};

int main()
{
    Pig myPig;
    myPig.animalSound();
    myPig.sleep();
}
```

- C#'s `abstract void AnimalSound();` ↔ C++'s `virtual void animalSound() = 0;` — both declare a method with no body that derived classes **must** implement.
- C#'s `abstract class` keyword has no direct C++ keyword — C++ just checks: "does this class contain any pure virtual function?" If yes, it's abstract (can't be instantiated), automatically.
- Both require the derived class to provide the implementation (`override` in both languages) before it can be instantiated.

---

## Interfaces

Another way of achieving abstraction is by **Interfaces**.

- An interface is a completely "abstract class", which can only contain abstract methods and properties.

```csharp
interface IAnimal
{
    void AnimalSound();
}

class Pig : IAnimal // Pig "implements" the IAnimal interface
{
    public void AnimalSound()
    {
        Console.WriteLine("The pig says: wee wee");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Pig myPig = new Pig();
        myPig.AnimalSound();
    }
}
```

- Like abstract classes, interfaces cannot be used to create objects.
- Interface methods don't have a body — the body is provided in the "implement" class.
- On implementation of an interface, you must override all of its methods.
- Interfaces can contain properties and methods, but not fields/variables.
- An interface cannot contain a constructor.

### Multiple Interfaces

To implement multiple interfaces, separate them with a comma:

```csharp
interface IFirst
{
    void MyMethod(); // interface method
}

interface ISecond
{
    void MyOtherMethod(); // interface method
}

class DemoClass : IFirst, ISecond
{
    public void MyMethod()
    {
        // ...
    }

    public void MyOtherMethod()
    {
        // ...
    }
}
```

---

## Enums

- An enum is a special "class" that represents a group of constants (unchangeable/read-only variables).
- To create an enum, use the `enum` keyword and separate the enum items with a comma.

### Example 1:

```csharp
enum Level
{
    Low,
    Medium,
    High
}
```

For accessing:
```csharp
Level myVar = Level.Medium;
Console.WriteLine(myVar);
```

> **Use enum** when you have values that you know aren't going to change.

### Example 2:

```csharp
class Program
{
    enum Level
    {
        Low,
        Medium,
        High = 4 // Subsequent change in the values
    }

    static void Main(string[] args)
    {
        Level myVar = Level.Medium;
        int myV = (int)Level.High;
        Console.WriteLine(myVar);
        Console.WriteLine(myV);
    }
}
```

**Output:**
```
Medium
4
```

---

## Files

- The `File` class from the `System.IO` namespace allows us to work with files.

```csharp
using System.IO; // include the System.IO namespace
File.SomeFileMethod(); // use the File class with its methods
```

- The `File` class has many useful methods:

| Method | Description |
|---|---|
| `AppendText()` | Appends text at the end of an existing file |
| `Copy()` | Copies a file |
| `Create()` | Creates or overwrites a file |
| `Delete()` | Deletes a file |
| `Exists()` | Tests whether the file exists |
| `ReadAllText()` | Reads the contents of a file |
| `Replace()` | Replaces the contents of a file with the contents of another file |
| `WriteAllText()` | Creates a new file and writes the contents to it. If the file already exists, it will be overwritten |

### Example:

```csharp
using System.IO;

string writeText = "Hello World!";
File.WriteAllText("filename.txt", writeText);

string readText = File.ReadAllText("filename.txt");
Console.WriteLine(readText);
```

---

## Exceptions

- When executing C# code, different errors can occur: coding errors made by the programmer, errors due to wrong input, etc.
- When an error occurs, C# will normally stop and generate an error message. The technical term for this is: C# will **throw an exception** (throw an error).

### C# try and catch

- The `try` statement allows you to define a block of code to be tested for errors while it is being executed.
- The `catch` statement allows you to define a block of code to be executed if an error occurs in the `try` block.
- The `try` and `catch` keywords come in pairs.

### Syntax:

```csharp
try
{
    // Block of code to try
}
catch (Exception e)
{
    // Block of code to handle errors
}
```

### Example:

```csharp
try
{
    int[] myNumbers = {1, 2, 3};
    Console.WriteLine(myNumbers[10]);
}
catch (Exception e)
{
    Console.WriteLine(e.Message);
}
```

**Output:** `Index was outside the bounds of the array.`

### Or to display own message:

```csharp
catch (Exception e)
{
    Console.WriteLine("Something went wrong");
}
```

### Finally

The `finally` statement lets you execute code after `try...catch`, regardless of the result.

```csharp
finally
{
    Console.WriteLine("The 'try catch' has ended.");
}
```

**Output:**
```
Something went wrong
The 'try catch' has ended
```

### Throw

- The `throw` keyword allows you to create a custom error.
- The `throw` statement is used together with an exception class. There are many exception classes available in C#: `ArithmeticException`, `FileNotFoundException`, `IndexOutOfRangeException`, etc.

### Example:

```csharp
static void CheckAge(int age)
{
    if (age < 18)
    {
        throw new ArithmeticException("Access denied - must be 18+");
    }
    else
    {
        Console.WriteLine("Access granted - old enough");
    }
}

static void Main(string[] args)
{
    CheckAge(15);
}
```

**Output:**
```
System.ArithmeticException: 'Access denied - must be 18+'
```
