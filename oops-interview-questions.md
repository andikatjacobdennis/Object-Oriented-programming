# Types of constructors in C#

### 1. **Default Constructor**

* A constructor that takes no parameters.
* Provided automatically if no constructors are defined.

```csharp
class Person
{
    public Person()
    {
        Console.WriteLine("Default constructor called");
    }
}
```

---

### 2. **Parameterized Constructor**

* Accepts parameters to initialize fields with custom values.

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

---

### 3. **Copy Constructor**

* Initializes a new object as a copy of an existing object.
* Not provided by default (you must define it manually).

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

---

### 4. **Static Constructor**

* Used to initialize static members of the class.
* Called **only once**, automatically, before any static members are accessed or any instance is created.
* Cannot take parameters or have access modifiers.

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

---

### 5. **Private Constructor**

* Used to prevent class instantiation from outside.
* Common in **singleton patterns** or static classes.

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

---

### 6. **Constructor Chaining (Using `this` and `base`)**

* Allows one constructor to call another in the same class or the base class.

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

