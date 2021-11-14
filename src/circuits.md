# Short Circuits
> "Oh dear," says God, "I hadn't thought of that," and promptly vanishes in a puff of logic.

Programming is sometimes called the art of laziness, but Python has laziness down to a science.

## Ternary conditionals
Imagine you are a Python coder. Just imagine it. If you *are* a Python programmer, this won't be particularly difficult. You might be able to imagine yourself coding an `if` statement.
```py
if condition:
    x = func()
else:
    x = 0
```
This can be shortened to one line using Python's handy-dandy ternary conditional syntax:
```py
x = func() if condition else 0
```
The syntax for `t if c else f` should be mostly self-explanatory, and it's an expression so it can be used in more positions than the traditional `if:`/`else:`. (The trade-off is that you can only put expressions in it, rather than full statements, but that is rarely a problem.)
```py
print(f"You're {'quite'if...else'not'} correct!")
# prints "You're quite correct!"
```

## `and` and/or `or`
A little secret: most of the time, ternary conditionals are not necessary. We've [seen](./truthiness.md) that Python has Boolean operators.
```py
a | b # a OR b
a & b # a AND b
a ^ b # a XOR b
~a # NOT a
```
Hold on, those aren't the operators from the previous page. They're bitwise operators, not Boolean. They shouldn't be here right now. Maybe if you ignore them they'll go away.
```py
a or b
a and b
a != b
not a
```
That's better. Now then, these Boolean operators are capable of emulating `if` and `else`, because of an optimisation called 'short-circuiting'. (The bitwise operators don't have that power.)

### Short circuiting
Look at an expression like `True or foo()`. `foo` might return `True` or `False`, but it doesn't matter which, because `True` `or` *anything* is always truthy. There's really no need to run `foo`. Python realises this information, and will instantly return `True` when it sees a `True` on the left of an `or`. Similarly, `False and foo()` is always `False`, so Python won't bother running `foo`.

(If you like, you can confirm this with the [truth table](./truthiness.md#booleans).)

In fact, `a or b` is like a shorter version of `a if a else b`, and `a and b` is like `a if not a else b`. (A slight difference: `foo() or b` calls `foo` once, while `foo() if foo() else b` calls it only once.) After staring at that notation, you might be wondering what happens with values that aren't `True` or `False`.

```py
1 or 0/0
# = 1 (doesn't raise `ZeroDivisionError`)

"" and print("something")
# = "" (doesn't print anything)

print("a") or "b"
# = 'b' (prints 'a' as well)
```
As you can see, it doesn't need to return Booleans.

That last example has practical merit. The `print` function, along with many other functions and methods that have side effects, returns `None`, which is falsy. That allows you to chain operations together: `my_list.append(number) or print(my_list) or number` is an expression. It evaluates to `number`, but it also appends `number` to a list and prints out that list.

If you know that `a()` always evaluates to something falsy, you can emulate `b() if c else a()` using `(c or a()) and b()`. It may not be any shorter, but it can often suggest other shortenings.

For instance, let's say you need the result of a function, except some of the time it returns a falsy value (`None`, `0`, `""` etc), and you need to replace those falsy return values with a default.
```py
line = fizz_or_buzz(n) if fizz_or_buzz(n) != "" else str(n)
# equivalently:
line = fizz_or_buzz(n) or str(n)
```

### Chained comparisons

Python automatically inserts the Boolean operator `and` into certain expressions.
```py
if 0 <= a < 10:
    print("in range")

# equivalent to:
if 0 <= a and a < 10:
    print("in range")
```
(Oh, alright. They're not quite equivalent, just like how Boolean operators aren't quite the same as ternary conditionals. The middle term is only evaluated once in a chained comparison, so `0 <= foo() < 10` would call `foo` once but `0 <= foo() and foo() < 10` would call it twice.)

```py
print(1 < 10 < 100 < 1000 < 10000)
# True

print(1 < 2 > 3)
# False

print(1000 < 42 <= 0/0)
# False (doesn't raise ZeroDivisionError because of Boolean short circuiting)
```
The comparison operators in Python are:
- `<` and `>`
- `==` and`!=`
- `<=` and `>=`
- `is` and `is not`
- `in` and `not in`

You can write `3 in (1, 2, 3) < (1, 2, 4) > (1, 2) not in [] != 1 is 1`, which is truthy. For extra fun, it will raise a `SyntaxWarning`.

## Golfer's corner
`and` is most useful for giving up. If your data processing only makes sense on truthy values, `and` will let you pass falsy ones through unprocessed. Though, as you've seen, `and` is not as short as `&` for comparing real Booleans, no matter how painful to admit.
