# Lambdas
> Yo dawg, I heard you like functions...

Given that you're reading a guide for writing esoteric Python, you probably know what functions are. The typical function is something like this:
```python
def foo(spam, eggs):
    # function body here
```
When golfing, that's a lot more characters than one would want. Luckily, there's an alternative; lambda functions.

## What is a lambda function?
A lambda function is a function that doesn't have a name, or an anonymous function. It can still be assigned to a variable, but it isn't given a name upon creation like a normal function.

## Syntax
Unlike a regular function, the entirety of a lambda function declaration is an expression. This means that a lambda can be declared inline. A lambda declaration is as follows:
```python
lambda spam, eggs: return_value
```
This resembles a normal function declaration, but there are a few differences; `def` is replaced with `lambda`, there is no function name, and there are no parentheses around the argument listing. Because a lambda declaration is an expression, you don't use the `return` keyword to return a value. Instead, the expression after the argument listing is what is evaluated and returned upon calling the lambda.

## Usage in esoteric python
Because lambdas are expressions, you can use them immediately after declaration, instead of having to assign to an intermediate variable. For example:
```python
map((lambda a: str(a)[:5]), somelist)
```
Here, instead of declaring a normal function and then passing it into the `map` call, we pass a lambda function, allowing that segment to be much shorter than it would be otherwise.
