# Index

1. [Why constructor injection is recommended](#introduction)
2. [Circular Dependency in Spring](#installation)
3. [Usage](#usage)
4. [API](#api)

---

# Introduction
This is introduction section.

# Installation
Steps to install.

# Usage
How to use the application.

# API
API documentation.


# Why constructor injection is recommended

**Constructor Injection is recommended** in frameworks like **Spring** because it provides **immutability, mandatory dependency injection, and better testability**.

### Short Interview Answer

You can say:

> In Dependency Inversion Principle, `UserService` depends on an abstraction like `Database`.
> The dependency is injected from outside, either manually in the main class or automatically using Spring IoC container through dependency injection.

---

## 1️⃣ Ensures Mandatory Dependencies

With constructor injection, the object **cannot be created without required dependencies**.

```java
class UserService {

    private final Database database;

    public UserService(Database database) {
        this.database = database;
    }
}
```

If `Database` is missing → **object creation fails immediately**.

But with **field injection**, object may be created with `null`.

```java
@Autowired
Database database;  // may cause NullPointerException later
```

---

## 2️⃣ Supports Immutability

Constructor injection allows `final` fields.

```java
private final Database database;
```

Once assigned, the dependency **cannot change**, making the class **safer and thread-safe**.

Field injection cannot use `final`.

---

## 3️⃣ Easier Unit Testing

You can easily inject **mock dependencies**.

```java
Database mockDb = new MockDatabase();

UserService service = new UserService(mockDb);
```

No Spring container required.

With field injection, testing becomes harder.

---

## 4️⃣ Prevents Circular Dependency Issues

Constructor injection helps detect **circular dependencies early at startup**, instead of failing later at runtime.

---

## 5️⃣ Recommended by Spring Team

Spring documentation itself recommends **constructor injection over field injection**.

---

### Circular Dependency in Spring

A **circular dependency** happens when **two or more beans depend on each other**.

Example:

```id="u9zzja"
A → depends on B
B → depends on A
```

Spring **cannot create the beans because each one needs the other first**.

---

## Example

```java id="h0db5k"
@Service
class A {

    private B b;

    public A(B b) {
        this.b = b;
    }
}
```

```java id="x7ty22"
@Service
class B {

    private A a;

    public B(A a) {
        this.a = a;
    }
}
```

Spring tries:

```id="y4qcdq"
Create A → needs B
Create B → needs A
```

This causes **BeanCurrentlyInCreationException**.

---

## Important Interview Point

| Injection Type           | Circular Dependency  |
| ------------------------ | -------------------- |
| Constructor Injection    | ❌ Not supported      |
| Setter / Field Injection | ✅ Spring can resolve |

Spring resolves setter injection using **early bean reference**.

---

# How to Fix Circular Dependency

### 1️⃣ Use `@Lazy`

```java id="l5i6s1"
@Service
class A {

    private B b;

    public A(@Lazy B b) {
        this.b = b;
    }
}
```

Spring creates a **proxy instead of the actual bean**.

---

### 2️⃣ Use Setter Injection

```java id="8p9qva"
@Service
class A {

    private B b;

    @Autowired
    public void setB(B b) {
        this.b = b;
    }
}
```

---

### 3️⃣ Redesign (Best Solution)

Usually circular dependency means **bad design**.

Better approach:

```id="qg30wp"
A → C
B → C
```

Introduce a **third service**.

---

## Short Interview Answer

You can say:

> Circular dependency occurs when two Spring beans depend on each other. With constructor injection Spring cannot resolve it and throws BeanCurrentlyInCreationException. It can sometimes resolve it using setter injection or @Lazy, but the best solution is redesigning the code to remove the circular dependency.

---



