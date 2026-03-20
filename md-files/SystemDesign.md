https://www.educative.io/courses/grokking-the-system-design-interview  
https://www.educative.io/courses/system-design-interview-prep-crash-course  
https://www.educative.io/courses/grokking-system-design-fundamentals  
https://www.educative.io/courses/system-design-deep-dive-real-world-distributed-systems  
https://www.educative.io/courses/advanced-system-design-interview-prep-crash-course  
https://www.educative.io/courses/system-design-interview-doordash  

Interviewers often ask this to see if you understand the **difference in scope and responsibility**.

---

## Q. System Design vs Software Architecture

| Aspect            | System Design                                       | Software Architecture                                              |
| ----------------- | --------------------------------------------------- | ------------------------------------------------------------------ |
| Scope             | Focus on designing a **specific system or feature** | Defines the **overall structure of the entire application/system** |
| Level             | More **detailed and implementation-oriented**       | More **high-level and strategic**                                  |
| Focus             | Scalability, data flow, APIs, database, caching     | Design principles, modules, technology choices                     |
| Time Horizon      | Solves **current system problem**                   | Guides **long-term system evolution**                              |
| Example Questions | Design URL shortener, design Uber                   | Microservices vs Monolith                                          |

---

# System Design Example

Problem:

```text
Design a URL Shortener (like bit.ly)
```

Things you decide:

* API design
* Database schema
* Caching
* Load balancing
* Scaling strategy

Example components:

```text
Client → Load Balancer → Application Server → Database
                     → Cache
```

This is **system design**.

---

# Software Architecture Example

Architecture decisions:

```text
Monolith vs Microservices
Event-driven architecture
Layered architecture
Hexagonal architecture
```

Example structure:

```text
Controller
   ↓
Service
   ↓
Repository
   ↓
Database
```

Architecture defines **how components are organized**.

---

# Simple Analogy

```text
Architecture → Blueprint of a city
System Design → Designing a specific building
```

Architecture decides:

* roads
* zones
* infrastructure

System design decides:

* rooms
* plumbing
* wiring

---

# Short Interview Answer

You can say:

> Software architecture defines the high-level structure and principles of the entire system, such as architecture patterns and technology choices. System design focuses on designing specific systems or components, including APIs, databases, scaling strategies, and data flow.

---


## Q. Monolithic vs Microservices Architecture

This question is common in **system design / architecture interviews**.

---

# 1️⃣ Monolithic Architecture

A **monolithic application** is a **single deployable unit** where all components are tightly integrated.

Example structure:

```text
Application
 ├── User Module
 ├── Order Module
 ├── Payment Module
 ├── Inventory Module
 └── Database
```

All modules run inside **one application**.

---

### Example (E-commerce)

```text
Single Spring Boot application
```

```text
Controller
   ↓
Service
   ↓
Repository
   ↓
Database
```

Everything deployed **together**.

---

### Advantages

✔ Simple to build initially
✔ Easy deployment
✔ Easier debugging
✔ Lower infrastructure complexity

---

### Disadvantages

❌ Hard to scale individual modules
❌ Large codebase becomes difficult to maintain
❌ One bug can affect entire system
❌ Technology lock-in

---

# 2️⃣ Microservices Architecture

Application is split into **multiple independent services**.

Example:

```text
User Service
Order Service
Payment Service
Inventory Service
```

Each service has:

```text
Own API
Own database
Independent deployment
```

Architecture:

```text
Client
  ↓
API Gateway
  ↓
-----------------------------
User Service
Order Service
Payment Service
Inventory Service
-----------------------------
```

---

### Advantages

✔ Independent deployment
✔ Independent scaling
✔ Better fault isolation
✔ Teams can work independently
✔ Technology flexibility

---

### Disadvantages

❌ Operational complexity
❌ Network latency
❌ Distributed system problems
❌ Harder debugging
❌ Data consistency challenges

---

# Comparison Table

| Feature           | Monolith           | Microservices       |
| ----------------- | ------------------ | ------------------- |
| Deployment        | Single application | Multiple services   |
| Scaling           | Whole app          | Individual services |
| Development       | Simple initially   | Complex             |
| Fault isolation   | Low                | High                |
| Technology choice | Single stack       | Multiple stacks     |

---

# When to Use Monolith

Good for:

* Startups
* Small teams
* Simple systems
* MVP products

Example:

```text
Startup e-commerce website
```

---

# When to Use Microservices

Good for:

* Large systems
* Large teams
* High scalability requirements

Example:

```text
Amazon
Netflix
Uber
Adyen
```

---

# Simple Interview Answer

You can say:

> Monolithic architecture is a single deployable application where all modules are tightly coupled. Microservices architecture splits the system into multiple independent services that communicate through APIs. Microservices improve scalability and fault isolation but introduce operational complexity.

---

