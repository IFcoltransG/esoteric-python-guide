# Lambdas
> Yo dawg, I heard you like functions...

## Introduction (a Segue to Decorators)
Functions are a staple of programming, and have been so for decades. Almost any language of a higher level than Assembly has functions, and Python is no exception. In Python, functions are what's called first class functions. This means that a function can be treated like any other variable, passed into other functions, used in expressions, etc. This fits nicely with Python's object oriented paradigm, where everything is an object. With this in mind, a new technique emerges; passing a function to another function which modifies the first function, and then using the modified function. This is, in essence, what a decorator does.

## Decorators

### What a decorator is
Decorators are syntactic sugar for the operation described above.
```py
def change_func(func):
    func.some_attribute = 5
    return func

# the following two are equivalent

@changefunc
def somefunc(x, y):
    return x + y

def somefunc(x, y):
    return x + y
somefunc = change_func(somefunc)
```

### Typical usage
The most common decorator uses are:
1. creating a wrapper for a function
2. modifying a class

`// here I intend to go into more detail about examples of decorator usage`

### Decorators take expressions
You don't just have to use a plain function name with a decorator, however. You can use any expression, as long as it evaluates to a callable.
`// here I intend to provide one or two examples of doing so`

### Convenience (a Segue to Lambdas)
Suppose you've going to make a decorator, but you're only ever going to use it once. You'd have a function that wouldn't ever be used again inhabiting the namespace. And, especially if the function does something small, function definitions are a little bit verbose, requiring multiple lines. Now, what if there was a way to define a function that isn't given a name, an anonymous function? This is what a lambda function is.

## Lambdas
Lambdas are convenient, small, and anonymous. If necessary, they can be assigned a name, using the usual assignment syntax, but they aren't given a name simply for being declared. Their declaration is an expression, so they can be declared and then used in the same place. And, because of how Python works, anything that can be done in a normal function can be done in a lambda.

For conditionals, use Python's ternary operator. For loops can be done with comprehension or generator expressions, builtin functions like `map`, etc. While loops can be done with a for loop and `iter`, and so on. To demonstrate this, the following is a function and its lambda equivalent:
```python
def somefunc(arg1, /, arg2, *args, **kwargs):
    if arg1:
        return arg2
    else:
        return kwargs[args[arg2]]

somefunc = lambda arg1, /, arg2, *args, **kwargs: arg2 if arg1 else kwargs[args[arg2]]
```
Those two are the same. They both create a function object, and assign it to the name "somefunc" in the current namespace. However, with normal functions the name assignment is done for you, but with lambdas, you have to do it yourself. This is because lambdas *don't have a name*. They're anonymous. However, they can still accomplish anything a normal python function can. For example, `import` statements can be done using the `__import__` function.
