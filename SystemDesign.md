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



