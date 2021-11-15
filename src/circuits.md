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

## Precedence
Python isn't a Lisp, so there's no reason to use extra brackets. If you keep in mind operator precedence, you can avoid adding redundant ones.

It can be easier to commit lists to memory with an explanation why they are in the order they are. You have probably seen that Python uses BEDMAS/PEMDAS/BODMAS/BIDMAS ordering, which means `a + (b * c)` is just `a + b * c`—Python always does the `*` before the `+`. None of these mnemonics have a letter for "Boolean AND" or "unary inverse" or "modulo", so it's just a skeleton of Python's operators.

If you'd like to follow along, there's a concise reference version in [the Python docs](https://docs.python.org/3/reference/expressions.html#operator-precedence).

### B/P
- First in the acronym is 'B' for 'brackets', because `(...)` is always the tightest grouping. Never can anything inside `(...)` pair itself with anything outside the brackets. This also goes for square brackets `[...]` and squiggly brackets `{...}`.
- After that, there's another use of brackets, which is calling functions `foo(...)` and indexing lists `foo[...]`. Attribute access is also at this level of precedence `foo.bar`.
- Next is the `await` keyword. It's looser than the above, so `await lib.load("utf-8")` is `await ( ( lib.load )("utf-8") )`.

  The operators so far have all operated on one value. In `foo()`, the `()` operates on just `foo`, whereas `foo + bar`, which comes later in the precedence chart, has `+` combining both `foo` and `bar` together. As a general rule, the single-value operations (like `await`) bind tighter than the two-value operators.
### E/O
- Every general rule has an exception, and exponentiation `**` is the exception to that one. It binds tighter than `-foo`, `~foo` and `+foo`, even though they're single-value operators and `a ** b` takes two values.

  It's also an exception to another rule: it's 'right-associative' rather than left-associative. That's another way of saying that Python treats `a ** b ** c ** d` as `a ** (b ** (c**d))`, which is the opposite order to every other two-valued operator. For instance, `a * b * c * d` turns into `((a*b) * c) * d`. Exponentiation probably gets its right-associativity from mathematical notation, where a power law, (a<sup>b</sup>)<sup>c</sup> = a<sup>b×c</sup>, makes left-associative exponents pointless.
- Of course next are `-foo`, `~foo`, `+foo`. (These are not their two-value forms: `+foo` is a different operator to `foo + bar`.) If you've ever typed `-1**2` into Google and got confused why it's `-1` rather than `(-1)**2` = `1`, this is why.
### D/M
- We're up to the 'D' and 'M' for division and multiplication. Python treats `*`, `@`, `/`, `//` and `%` as forms of multiplication and division, so they all have the same precedence.
### A/S
- 'A' and 'S'—adding and subtracting—are uncomplicated. `foo + bar` and `foo - bar` are the loosest operators yet.
### Bitwise
- The mnemonics end here, and this is the point where primary school students would stop learning and go outside. They didn't have to learn about bitwise operations, starting with left-shift `<<` and right-shift `>>`. The shifts are at this precedence because they're often used to prepare values for the other bitwise operators.
- After that are `&`, `^` and `|`, in that order. The reason `&` (*bitwise AND*) and `|` (*bitwise OR*) go in this order, is that mathematicians think of `&` and `|` as a lot like `*` and `+`, respectively—that's why `a * b` gives you the same Boolean as `a & b` if (`a` and `b` are Booleans) and `bool(a + b)` gives you the same as `a | b`.

  So to keep the mathematicians happy, `a | b & c` has analogous operator precedence to `a + b * c`: it's `a | (b & c)`. (For some reason *bitwise XOR* `^` is put between these operators' precedence.)
### Boolean
- You've already had a look at the comparison operators. They all go here. That's why `a + b >= c` doesn't accidentally parse as `a + (b >= c)` which would be less intuitive than `(a + b) >= c`. Python combines all of the numerical and string operations at a tighter precedence than Booleans, because lots of Boolean expressions have numerical operations like addition inside them.
- Intuition says that `a >= b or b >= c` should parse as `(a >= b) or (a >= c)`, so the Boolean operators go next. The first Boolean operator is the one-valued Boolean operator, `not a`, because operations on a single value come before operations on two values. Then the two-valued operators, `a and b` and `a or b`. Just like `&` and `|`, `and` binds more tightly than `or` does.
- What does Python expect you to do with Booleans?

  —Use them in an `if`/`else`!

  So `a if b else c` has to be looser than everything else so far.

  Did you know that 'ternary' in "ternary conditional" means "three-valued," because it takes an `a`, a `b` and a `c`? Given the pattern of single-valued operators before double-, it fits that a three-valued operator should go after all the other Boolean operations.
### Beyond
- The penultimate on the list is `lambda ...: foo`, which is designed so that almost any expression can go on its right-hand-side.
- The only expression you can't put on the right of a lambda is anything using walrus assignment, `:=`, because Python thought that would be useless.

Phew! That's all of them. Every so often you can use these precedence rules to shave off a few brackets. It helps to keep them with you always. Post-it them to the back of your laptop. Tattoo them on your wrist. Whisper them as you fall asleep.

## Golfer's corner
Aren't Boolean operators nice? So much statelier than the bitwise operators, you may well find.

They are lazy though, it must be said. `and` is most useful for giving up. If your data processing only makes sense on truthy values, `and` will let you pass falsy ones through unprocessed. Though, as you've seen, `and` is not as short as `&` for comparing real Booleans, no matter how painful to admit.

`and` might be better than `&` if you need the implicit Boolean conversions, when you want to return an arbitrary object, for its short-circuiting behaviour, or if it has a helpful precedence that lets you trim brackets.
