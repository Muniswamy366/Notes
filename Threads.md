
* `Semaphore vs Mutex`
* `ReentrantLock`
* `CountDownLatch vs CyclicBarrier`
* `ThreadPoolExecutor`

* 
Thread Starvation
Deadlock in Java

volatile vs synchronized

Runnable vs Callable

CompletableFuture

ThreadPoolExecutor


### Java’s CompletableFuture Writing Non-Blocking Code Made Easy! 💡

Writing asynchronous and non-blocking code is not just an optimization — it’s a requirement.  

💡 What is CompletableFuture?  

CompletableFuture is part of Java’s java.util.concurrent package and helps you:  

✅ Run tasks asynchronously   
✅ Chain multiple tasks without blocking  
✅ Combine multiple futures  
✅ Handle errors gracefully  

🧪 Real-World Example – Calling Two APIs in Parallel  

Imagine a backend service that needs to fetch user profile data and transaction history from two different services:  
```
CompletableFuture<User> userFuture = CompletableFuture.supplyAsync(() -> userService.getUser(userId));
CompletableFuture<List<Txn>> txnFuture = CompletableFuture.supplyAsync(() -> txnService.getTransactions(userId));

CompletableFuture<CombinedResponse> result = userFuture.thenCombine(txnFuture, (user, txns) ->
 new CombinedResponse(user, txns)
);

result.thenAccept(response -> {
 System.out.println("Final response: " + response);
});
```

🧵 Both tasks run in parallel, improving performance without blocking any thread.  

📌 Commonly used CompletableFuture methods:  

supplyAsync() – Run a task asynchronously and return a value  
runAsync() – Run a task asynchronously that returns no result  
thenApply() – Transform the result when it’s ready  
thenAccept() – Consume the result (no return)  
thenRun() – Run a task after another completes (no access to result)  
thenCombine() – Combine results of two independent futures  
thenCompose() – Chain futures where the second depends on the first  
exceptionally() – Handle exceptions with a fallback  
handle() – Process result or exception in one place  
allOf() / anyOf() – Wait for all or any of multiple futures  

```
CompletableFuture.supplyAsync(() -> {
    if (true) throw new RuntimeException("Fail");
    return "OK";
}).exceptionally(ex -> "Recovered: " + ex.getMessage())
  .thenAccept(System.out::println);
```

Example with CompletableFuture – Non-blocking
```
CompletableFuture.supplyAsync(() -> {
    try { Thread.sleep(1000); } catch (Exception e) {}
    return "Hello CompletableFuture!";
}).thenAccept(result -> {
    System.out.println(result);  // Non-blocking callback
});
```
Chaining
```
CompletableFuture.supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")
    .thenAccept(System.out::println);  // prints: Hello World
```
Combining Two Futures
```
CompletableFuture<String> first = CompletableFuture.supplyAsync(() -> "Java");
CompletableFuture<String> second = CompletableFuture.supplyAsync(() -> "Future");

CompletableFuture<Void> combined = CompletableFuture.allOf(first, second);

combined.thenRun(() -> {
    System.out.println(first.join() + " " + second.join());  // Output: Java Future
});
```

### Volatile in Java
The volatile keyword in Java ensures visibility and ordering of variable changes across threads.  

Key Guarantees:  
Visibility: When one thread writes to a volatile variable, the change is immediately visible to all other threads  

Happens-Before: Writes to volatile variables happen-before subsequent reads (prevents instruction reordering)  

No Caching: Threads always read from main memory, not from CPU cache  

When to Use:  
Flag variables that control thread execution  

Simple state indicators read by multiple threads  

When you need visibility but NOT atomicity of compound operations  

What volatile Does NOT Do:  
❌ Does NOT make compound operations atomic (e.g., count++)  

❌ Does NOT provide mutual exclusion (use synchronized or locks)  

❌ Does NOT guarantee atomicity for non-atomic operations  

When NOT to Use:  
```
private volatile int count = 0;
count++;  // NOT thread-safe! (read-modify-write is 3 operations)
```

Use AtomicInteger or synchronized instead for compound operations.  

Performance:
Cheaper than synchronized (no locking overhead)  

More expensive than regular variables (prevents optimizations)  

Good for read-heavy scenarios with occasional writes  

## Q. Semaphore
### Semaphore in Java

A **Semaphore** is a synchronization mechanism that **controls how many threads can access a resource at the same time**.

It is available in:

```java
java.util.concurrent
```

Think of it like **parking slots** 🚗
If there are **3 slots**, only **3 cars (threads)** can enter at once.

---

## Example

```java
import java.util.concurrent.Semaphore;

public class SemaphoreExample {

    static Semaphore semaphore = new Semaphore(2); // only 2 threads allowed

    public static void main(String[] args) {

        Runnable task = () -> {
            try {
                semaphore.acquire(); // acquire permit
                System.out.println(Thread.currentThread().getName() + " acquired permit");

                Thread.sleep(2000);

                System.out.println(Thread.currentThread().getName() + " releasing permit");

            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                semaphore.release(); // release permit
            }
        };

        for(int i = 0; i < 5; i++) {
            new Thread(task).start();
        }
    }
}
```

### Output behavior

Only **2 threads run simultaneously**, others wait.

```
Thread-1 acquired permit
Thread-2 acquired permit
Thread-3 waits
Thread-4 waits
Thread-5 waits
```

---

## Important Methods

| Method               | Description                       |
| -------------------- | --------------------------------- |
| `acquire()`          | Thread waits for permit           |
| `release()`          | Release permit                    |
| `tryAcquire()`       | Try to get permit without waiting |
| `availablePermits()` | Number of permits left            |

---

## Fair vs Non-Fair Semaphore

```java
Semaphore semaphore = new Semaphore(3, true);
```

`true` → **fair semaphore**

Threads get permits in **FIFO order**.

Default → **non-fair** (better performance).

---

## Real-World Use Cases

* **Database connection pools**
* **Rate limiting**
* **Limiting concurrent API calls**
* **Resource pooling**
* **Thread throttling**

Example:

```text
Only 10 users can access API simultaneously
```

---

## Semaphore vs Lock vs CountDownLatch

| Feature                   | Semaphore | Lock | CountDownLatch |
| ------------------------- | --------- | ---- | -------------- |
| Control number of threads | ✅         | ❌    | ❌              |
| Mutual exclusion          | ❌         | ✅    | ❌              |
| Wait for tasks to finish  | ❌         | ❌    | ✅              |

---

## Short Interview Answer

You can say:

> Semaphore is a synchronization utility that controls access to a shared resource by multiple threads using permits. Threads acquire a permit before accessing the resource and release it afterward. It is commonly used to limit concurrent access to resources like database connections.

---


