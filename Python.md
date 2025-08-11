In Python, functions are "first class citizens," meaning they are treated like any other object. This allows you to:  

* Assign functions to variables
* Pass functions as arguments to other functions
* Return functions from other functions
* Store functions in data structures like lists or dictionaries
For example:  

```
def greet(name):
    return f"Hello, {name}!"

say_hello = greet  # Assign function to a variable
print(say_hello("Alice"))  # Output: Hello, Alice!

def call_func(func, value):
    return func(value)

print(call_func(greet, "Bob"))  # Passing function as argument
```
