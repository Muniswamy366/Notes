
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
