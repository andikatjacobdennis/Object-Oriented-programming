# Types of constructors in C#

## 1. **Default Constructor**

- A constructor that takes no parameters.
- Provided automatically if no constructors are defined.

```csharp
class Person
{
    public Person()
    {
        Console.WriteLine("Default constructor called");
    }
}
```

## 2. **Parameterized Constructor**

- Accepts parameters to initialize fields with custom values.

```csharp
class Person
{
    public string Name;
    public int Age;

    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }
}
```

## 3. **Copy Constructor**

- Initializes a new object as a copy of an existing object.
- Not provided by default (you must define it manually).

```csharp
class Person
{
    public string Name;
    public int Age;

    public Person(Person other)
    {
        Name = other.Name;
        Age = other.Age;
    }
}
```

## 4. **Static Constructor**

- Used to initialize static members of the class.
- Called **only once**, automatically, before any static members are accessed or any instance is created.
- Cannot take parameters or have access modifiers.

```csharp
class Config
{
    public static string Setting;

    static Config()
    {
        Setting = "Initialized";
    }
}
```

## 5. **Private Constructor**

- Used to prevent class instantiation from outside.
- Common in **singleton patterns** or static classes.

```csharp
class Singleton
{
    private static Singleton instance = null;

    private Singleton() { }

    public static Singleton GetInstance()
    {
        if (instance == null)
            instance = new Singleton();
        return instance;
    }
}
```

## 6. **Constructor Chaining (Using `this` and `base`)**

- Allows one constructor to call another in the same class or the base class.

**Using `this`:**

```csharp
class Person
{
    public string Name;
    public int Age;

    public Person() : this("Unknown", 0) { }

    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }
}
```

**Using `base`:**

```csharp
class Animal
{
    public Animal(string type)
    {
        Console.WriteLine("Animal: " + type);
    }
}

class Dog : Animal
{
    public Dog() : base("Dog") { }
}
```

# Difference between class and struct

- Structs are **copied by value**, while classes are **referenced by reference**.
- Structs cannot **inherit** from other structs or classes.
- Structs cannot have **explicit parameterless constructors**, but they do have an **implicit one**.
- Structs must be **nullable** to assign `null`.
- **Boxing/unboxing** is necessary when using structs as objects.
- Mutating a struct does not affect the original (unless passed by reference).
- Classes reside on the **heap**, structs on the **stack** (unless boxed or in a class).

## Code Example

```csharp
using System;

namespace StructVsClassDemo
{
    // CLASS EXAMPLE
    class Person
    {
        public string Name;
        public int Age;

        public Person(string name, int age)
        {
            Name = name;
            Age = age;
        }

        public void ChangeName(string newName)
        {
            Name = newName;
        }
    }

    // STRUCT EXAMPLE
    struct Point
    {
        public int X;
        public int Y;

        // Parameterized constructor is allowed
        public Point(int x, int y)
        {
            X = x;
            Y = y;
        }

        public void Move(int dx, int dy)
        {
            X += dx;
            Y += dy;
        }

        public override string ToString()
        {
            return $"({X}, {Y})";
        }
    }

    // CLASS INHERITANCE (VALID)
    class Animal
    {
        public virtual void Speak()
        {
            Console.WriteLine("Animal sound");
        }
    }

    class Dog : Animal
    {
        public override void Speak()
        {
            Console.WriteLine("Woof!");
        }
    }

    // STRUCT INHERITANCE (INVALID - UNCOMMENTING WILL CAUSE ERROR)
    /*
    struct MyStruct : Animal
    {
        // Error: Structs cannot inherit from classes
    }
    */

    class Program
    {
        static void Main(string[] args)
        {
            // === 1. Value vs Reference Type Behavior ===
            Point p1 = new Point(1, 1);
            Point p2 = p1;   // Value copy

            p2.X = 100;
            Console.WriteLine($"Struct - p1: {p1}, p2: {p2}");  // p1 unchanged

            Person person1 = new Person("Alice", 30);
            Person person2 = person1;   // Reference copy

            person2.Name = "Bob";
            Console.WriteLine($"Class - person1: {person1.Name}, person2: {person2.Name}");  // both changed

            // === 2. Inheritance ===
            Animal a = new Dog();
            a.Speak();  // "Woof!" - shows polymorphism

            // === 3. Null Assignment ===
            Person pClass = null;
            Console.WriteLine(pClass == null ? "Class can be null" : "Not null");

            Point? nullableStruct = null;  // Must use nullable if assigning null
            Console.WriteLine(nullableStruct.HasValue ? "Has value" : "Struct can be nullable using ?");

            // === 4. Boxing/Unboxing ===
            Point boxablePoint = new Point(3, 3);
            object boxed = boxablePoint;  // Boxing
            Point unboxed = (Point)boxed; // Unboxing
            Console.WriteLine($"Boxed/unboxed struct: {unboxed}");

            // === 5. Mutability ===
            Point mutablePoint = new Point(2, 2);
            mutablePoint.Move(1, 1);
            Console.WriteLine($"Moved point: {mutablePoint}");

            // === 6. Default Constructor ===
            // Person class requires constructor call
            Person defaultPerson = new Person("Default", 0);

            // Struct has implicit default constructor
            Point defaultPoint = new Point();  // X and Y will be 0
            Console.WriteLine($"Default point: {defaultPoint}");

            // === 7. Stack vs Heap (Observe in Debugger) ===
            // Not directly visible in code, but in memory:
            // - Person lives on the heap
            // - Point lives on the stack (unless boxed or in a class)
        }
    }
}
```

## Output

```
Struct - p1: (1, 1), p2: (100, 1)
Class - person1: Bob, person2: Bob
Woof!
Class can be null
Struct can be nullable using ?
Boxed/unboxed struct: (3, 3)
Moved point: (3, 3)
Default point: (0, 0)
```

# Difference Between Class and Interface in C#

| Feature                  | **Class**                                                                                             | **Interface**                                                                                                                |
| ------------------------ | ----------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **Definition**           | A blueprint for creating objects that can contain **fields, methods, constructors, properties**, etc. | A contract that defines **method and property signatures only** (no implementation unless using default methods in C# 8.0+). |
| **Implementation**       | Can be instantiated (if not abstract).                                                                | Cannot be instantiated; must be implemented by a class or struct.                                                            |
| **Members**              | Can contain fields, constructors, methods (with or without implementation), properties, events, etc.  | Can contain only **method/property/event/indexer signatures** (until C# 8.0+ added default implementations).                 |
| **Access Modifiers**     | Can use `public`, `private`, `protected`, etc.                                                        | Members are **public** by default; modifiers are not allowed.                                                                |
| **Inheritance**          | Supports **single inheritance** (a class can inherit from one class).                                 | Supports **multiple inheritance** (a class can implement multiple interfaces).                                               |
| **Constructors**         | Can define constructors.                                                                              | Cannot define constructors.                                                                                                  |
| **Fields**               | Can contain fields (variables).                                                                       | Cannot contain instance fields.                                                                                              |
| **Abstract vs Concrete** | Can be abstract or concrete.                                                                          | Always abstract (unless using default implementations).                                                                      |
| **Use Case**             | Used to define the full behavior/state of an object.                                                  | Used to define **capabilities** or **contracts** that multiple classes can share.                                            |

## Code Example

```csharp
using System;

namespace ClassVsInterfaceDemo
{
    // INTERFACE: Cannot have implementation before C# 8, but allowed now (with default methods)
    interface IVehicle
    {
        void Start();
        void Stop();
    }

    // ANOTHER INTERFACE: for multiple inheritance
    interface IElectric
    {
        void Charge();
        int BatteryLevel { get; set; }
    }

    // BASE CLASS: can contain fields, constructors, methods, etc.
    class Vehicle
    {
        public string Brand;
        protected int Speed;

        public Vehicle(string brand)
        {
            Brand = brand;
            Speed = 0;
        }

        public void Accelerate(int increment)
        {
            Speed += increment;
            Console.WriteLine($"{Brand} accelerated to {Speed} km/h.");
        }

        public void Brake()
        {
            Speed = 0;
            Console.WriteLine($"{Brand} stopped.");
        }
    }

    // DERIVED CLASS: inherits from one base class and implements multiple interfaces
    class ElectricCar : Vehicle, IVehicle, IElectric
    {
        public int BatteryLevel { get; set; }

        // Constructor calls base class constructor
        public ElectricCar(string brand) : base(brand)
        {
            BatteryLevel = 100;
        }

        // Implementing interface methods
        public void Start()
        {
            Console.WriteLine($"{Brand} is starting silently...");
        }

        public void Stop()
        {
            Console.WriteLine($"{Brand} has stopped.");
        }

        public void Charge()
        {
            BatteryLevel = 100;
            Console.WriteLine($"{Brand} is charging. Battery full.");
        }

        // Additional method
        public void ShowStatus()
        {
            Console.WriteLine($"Status - Brand: {Brand}, Battery: {BatteryLevel}%");
        }
    }

    // Another class implementing only IVehicle
    class Bicycle : IVehicle
    {
        public void Start()
        {
            Console.WriteLine("Bicycle ride started.");
        }

        public void Stop()
        {
            Console.WriteLine("Bicycle stopped.");
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // === CLASS VS INTERFACE BEHAVIOR ===

            // 1. Class with constructor, fields, methods
            Vehicle genericVehicle = new Vehicle("Generic");
            genericVehicle.Accelerate(30);
            genericVehicle.Brake();

            Console.WriteLine();

            // 2. Class that inherits from another class and implements interfaces
            ElectricCar tesla = new ElectricCar("Tesla");
            tesla.Start();                // from IVehicle
            tesla.Accelerate(60);        // from Vehicle
            tesla.ShowStatus();
            tesla.Charge();              // from IElectric
            tesla.Stop();                // from IVehicle

            Console.WriteLine();

            // 3. Class implementing interface only (no inheritance)
            Bicycle bike = new Bicycle();
            bike.Start();
            bike.Stop();

            Console.WriteLine();

            // 4. Interface cannot be instantiated
            // IVehicle iv = new IVehicle(); // Error: cannot create an instance of an interface

            // 5. Multiple interface implementation
            IVehicle ivTesla = tesla;
            IElectric ieTesla = tesla;
            ivTesla.Start();
            Console.WriteLine($"Battery: {ieTesla.BatteryLevel}%");

            // 6. Fields: Only classes have fields
            // Interfaces like IVehicle cannot contain fields (but can have properties)
        }
    }
}
```

## Output

```
Generic accelerated to 30 km/h.
Generic stopped.

Tesla is starting silently...
Tesla accelerated to 60 km/h.
Status - Brand: Tesla, Battery: 100%
Tesla is charging. Battery full.
Tesla has stopped.

Bicycle ride started.
Bicycle stopped.

Tesla is starting silently...
Battery: 100%
```

# Demonstrates subtle differences between `override`, `new`, and base class calls

## Code Example

```csharp
using System;

namespace OOPAdvancedDemo
{
    // Abstract Base Class
    abstract class A
    {
        public A()
        {
            Console.WriteLine("Constructor A");
        }

        public abstract void AbstractMethod();

        public virtual void VirtualMethod()
        {
            Console.WriteLine("A.VirtualMethod");
        }

        public void NormalMethod()
        {
            Console.WriteLine("A.NormalMethod");
        }

        public virtual void Show()
        {
            Console.WriteLine("A.Show");
        }
    }

    // Intermediate Class
    class B : A
    {
        public B()
        {
            Console.WriteLine("Constructor B");
        }

        public override void AbstractMethod()
        {
            Console.WriteLine("B.AbstractMethod");
        }

        public override void VirtualMethod()
        {
            Console.WriteLine("B.VirtualMethod (overridden)");
        }

        public new void NormalMethod() // Method hiding
        {
            Console.WriteLine("B.NormalMethod (hidden)");
        }

        public new virtual void Show() // Hiding A.Show and making virtual again
        {
            Console.WriteLine("B.Show (new virtual)");
        }

        public virtual void OnlyInB()
        {
            Console.WriteLine("B.OnlyInB");
        }
    }

    // Most Derived Class
    class C : B
    {
        public C()
        {
            Console.WriteLine("Constructor C");
        }

        public override void VirtualMethod()
        {
            Console.WriteLine("C.VirtualMethod (overridden)");
        }

        public new void NormalMethod()
        {
            Console.WriteLine("C.NormalMethod (hidden again)");
        }

        public override void Show()
        {
            Console.WriteLine("C.Show (overridden)");
        }

        public sealed override void OnlyInB()
        {
            Console.WriteLine("C.OnlyInB (sealed override)");
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("=== C obj = new C(); ===");
            C objC = new C(); // Constructor chaining

            Console.WriteLine("\n=== A refA = new C(); ===");
            A refA = new C();

            Console.WriteLine("\n=== B refB = new C(); ===");
            B refB = new C();

            Console.WriteLine("\n=== Method Calls ===");

            Console.WriteLine("\nrefA.VirtualMethod():");
            refA.VirtualMethod();  // Virtual method - resolved at runtime

            Console.WriteLine("\nrefA.Show():");
            refA.Show();           // A.Show is virtual, B hides it with `new`, but not override, so A.Show is called

            Console.WriteLine("\nrefB.Show():");
            refB.Show();           // B.Show is virtual (new), C overrides it

            Console.WriteLine("\nobjC.Show():");
            objC.Show();           // C.Show

            Console.WriteLine("\nrefB.NormalMethod():");
            refB.NormalMethod();   // B.NormalMethod (hidden from A)

            Console.WriteLine("\n((A)refB).NormalMethod():");
            ((A)refB).NormalMethod();  // A.NormalMethod

            Console.WriteLine("\nrefB.OnlyInB():");
            refB.OnlyInB();        // C override (sealed)
        }
    }
}
```
## Output

```
=== C obj = new C(); ===
Constructor A
Constructor B
Constructor C

=== A refA = new C(); ===
Constructor A
Constructor B
Constructor C

=== B refB = new C(); ===
Constructor A
Constructor B
Constructor C

=== Method Calls ===

refA.VirtualMethod():
C.VirtualMethod (overridden)

refA.Show():
A.Show

refB.Show():
C.Show (overridden)

objC.Show():
C.Show (overridden)

refB.NormalMethod():
B.NormalMethod (hidden)

((A)refB).NormalMethod():
A.NormalMethod

refB.OnlyInB():
C.OnlyInB (sealed override)
```
