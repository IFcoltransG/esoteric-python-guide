# Truthiness and Falsiness
> What's true for you doesn't have to be true forever, and sometimes the truth isn't always for the better.

Humanity has been trying to figure out the nature of truth for millennia. Philosophy tells us that some sentences are neither true nor false, such as this one. Fortunately, Python doesn't care what philosophers think.

## Booleans

Booleans start simple enough: they can either be `True` or `False`. In the same way that `1 + 1` equals `2`, `1 + 1 == 2` equals `True`. Python seems to respect mathematics more than philosophy, and there's a whole branch of algebra dedicated to combining `True` and `False` with the operators `and`, `or` and `not`. (An interesting coincidence: the mathematician who invented Boolean algebra, George Boole, was named after Booleans.)

`and`, `not` and `or` are pretty intuitive, though `or` is inclusive, meaning `True or True` is `True`.

<details> <summary> <i> Expand for the full truth table. </i> </summary>
Here is a lookup table for any <code>A</code> and any <code>B</code>.
<table>
  <tr>
    <th>A</th><th>B</th><th>not A</th><th>not B</th><th>A and B</th><th>A or B</th> </tr>
  <tr>
    <th>False</th><th>False</th><td>True</td><td>True</td><td>False</td><td>False</td> </tr>
  <tr>
    <th>False</th><th>True</th><td>True</td><td>False</td><td>False</td><td>True</td><tr>
    <th>True</th><th>False</th><td>False</td><td>True</td><td>False</td><td>True</td> </tr>
  <tr>
    <th>True</th><th>True</th><td>False</td><td>False</td><td>True</td><td>True</td> </tr>
  </tr>
</table>
</details>

It is almost never necessary to write `x == True` or `x == False`. `x` and `not x` are cleaner, which may or may not be a good thing. You should also be careful when returning Booleans in `if` statements:

```python
if x is True:
    return True
else:
    return False
# returns True when x is True, and False when x is False
```
You can simply write `return x`. You probably don't need to be warned that `not not` is pointless.

### Weirder

Perhaps you've noticed that Booleans look a lot like bits in binary. `False` seems like `0` and `True` like `1`. In fact, before Booleans were added, Python used `0` and `1` instead. This isn't just history trivia: Python still treats Booleans as numbers for backwards compatibility. Yes, that means `True + 1` is `2`. You can use a `bool` object any time you need an `int` `0` or `1`. This means you can write `not x` as `1>x`, which is shorter and might cut out some whitespace around the expression.

## Truthiness

What happens when Python expects a Boolean, but you give it a small archipelago? Probably not an error.

Python treats everything, not just Booleans, as either 'truthy' or 'falsy' (sometimes 'falsey'). If you put something truthy in an `if` condition, it will run the block, but if it's falsy, the `else` block will run instead.

Obviously `True` is truthy, but every other object is also truthy by default. The exceptions are `None`, zero, empty collections and, unsurprisingly, `False`. The zeroes are `0`, `0.0` and `0j`. Empty collections include empty strings, empty lists, empty dictionaries, empty `frozenset`s, empty tuples... you get the idea.


```python
numbers = [1, 2, 3, 4, 5]
# loop until list is empty
while numbers: # truthy if numbers has elements, falsy otherwise
    print(numbers.pop())

n = 10
# loop until n is 0
while n:
    n -= 1

# the str version is no different
```

This makes conversion between Booleans and integers entirely implicit. You can use a XOR designed for integers on Booleans if you want: `a ^ b`.

`boolean<1` might seem like a short way to negate a Boolean, but if can be even shorter if you are prepared to do some setup first. The `~` operator converts `0` to `-1` and `-1` to `0`, among other things. `-1` is not zero, so it's truthy. That means if you negate your Boolean beforehand (`f = -f`, turning `True` to `-1` and `False` to `0`) then you can simply use `~f` instead of `not f`.

## Algebra

Knowing Boolean algebra won't do you any harm but only one part is essential: De Morgan's Law. `(not a) or (not b)` is always equal to `not (a and b)`. Likewise, `(not a) and (not b)` is `not (a or b)`.

<details> <summary> <i> Expand for another way of putting it. </i> </summary> You probably use De Morgan's Law every day. If you are not <i>rich and famous</i>, then you are either <i>not rich</i>, or <i>not famous</i>. If you are not <i>hungry or thirsty</i>, then you must be both <i>not hungry</i>, and <i>not thirsty</i>.
<p>
<p>
In other words, if you have a <code>not</code> outside an <code>and</code> or <code>or</code> expression, you can bring it inside of the expression by negating both operands <code>a</code> and <code>b</code>, then swapping <code>and</code> with <code>or</code>. Equivalently, you can negate both operands if you also negate the whole expression and swap the <code>and</code>/<code>or</code>. (Make sure you know where your brackets are, so you don't accidentally negate <code>a</code> rather than the whole expression.) Here's a demonstration that <code>not (A and B)</code> is <code>(not A) or (not B)</code>:
<table>
  <tr>
    <th>A</th><th>B</th><th>not A<br></th><th>not B<br></th><th>A and B<br></th><th>not (A and B)<br></th><th>(not A) or (not B)<br></th> </tr>
  <tr>
    <th>False</th><th>False</th><td>True</td><td>True</td><td>False</td><td>True</td><td>True</td> </tr>
  <tr>
    <th>False</th><th>True</th><td>True</td><td>False</td><td>False</td><td>True</td><td>True</td> </tr>
  <tr>
    <th>True</th><th>False</th><td>False</td><td>True</td><td>False</td><td>True</td><td>True</td> </tr>
  <tr>
    <th>True</th><th>True</th><td>False</td><td>False</td><td>True</td><td>False</td><td>False</td> </tr>
</table>
And here's <code>not (A or B)</code> is <code>(not A) and (not B)</code>:
<table>
  <tr>
    <th>A</th><th>B</th><th>not A<br></th><th>not B<br></th><th>A or B<br></th><th>not (A or B)<br></th><th>(not A) and (not B)<br></th> </tr>
  <tr>
    <th>False</th><th>False</th><td>True</td><td>True</td><td>False</td><td>True</td><td>True</td> </tr>
  <tr>
    <th>False</th><th>True</th><td>True</td><td>False</td><td>True</td><td>False</td><td>False</td> </tr>
  <tr>
    <th>True</th><th>False</th><td>False</td><td>True</td><td>True</td><td>False</td><td>False</td> </tr>
  <tr>
    <th>True</th><th>True</th><td>False</td><td>False</td><td>True</td><td>False</td><td>False</td> </tr>
</table>
</details>


## Pragmatism
If you need to select an expression based on a Boolean, indexing `(a,b)[boolean]` is often shorter than an explicit [ternary conditional](./circuits.md#ternary-conditionals) `b if boolean else a`. The problem with the former is that it will always evaluate both `a` and `b`. For example, `(print("a"),print("b"))[boolean]` will always print both `a` and `b`, so should be converted to `print(("a","b")[boolean])`  or `print("ab"[boolean])` to print only one of the two. (Many other situations can be shortened using short-circuiting.)

If you need to convert a Boolean to lowercase text `"true"`/`"false"`, you can interleave the two strings and use a slice, saving on quote marks. `"ftarlusee"[boolean::2]` takes every second character, starting at either `0` or `1` depending, so will suffice. The trick works for any pair of strings that are the same length, or if there's one extra character for the `False` option.
