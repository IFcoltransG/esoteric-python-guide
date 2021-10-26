# Whitespace
> They'll tell you I'm insane, but I've got a blank space, baby, and I'll write your name.

You've probably heard that Python has significant whitespace, but whitespace has special significance to any esoteric pythonista.

## Indentation
Unlike many programmers, Python won't fight you about whether you use tabs or spaces. If you replace all of the spaces and tabs in a Python file with each other, your code should still run. Using `·` for space and `↹` for tab, the following is valid Python:
```python
if↹True:
·print(end="Hello")
·print("",↹"World")
```
You can combine both in one indentation level, if you're careful to always use the same mix of spaces and tabs:
```python
if True:
↹·↹·↹a = 1
↹·↹·↹if a < 10:
↹·↹·↹···a = 10
↹·↹·↹···print(a)
```
However, the following will *not* work, because the order of the tab and space are different:
```python
if True:
↹·a = 1
·↹b = 2 # IndentationError: unindent does not match any outer indentation level
```
## Separators
Python is lax with whitespace, letting you put it in strange places and leave it out in stranger ones. Whenever Python thinks it can unambiguously figure out where a token ends, you don't need whitespace following. For instance:
```python
print(arg1,arg2,kwarg=kwarg)
for(a)in[1]:b=a
print(1and 2in[]) #keywords and variables can't begin with digits
foo=lambda*args:lambda arg,/:args+[arg]
```
This is a godsend for golfing. Never put spaces around your operators, and try to manoeuvre your terms so that any keywords are right next to punctuation. For instance, checking if a number is odd, `if 1-i%2:` can be `if~i%2:` instead.

### Numbers

Spaces can't go in numeric literals. Python views `1.__eq__` as `(1.) __eq__` or `(1.0) __eq__`, which is a syntax error. You can fix it with a space: `1 .__eq__` is the dunder method for `== 1` because `1 .` isn't a valid float literal.

If you want to put a space in a long number for readability, you're reading the wrong guide. However, `_` can be used instead, meaning `1_000_000` is a valid number.

### Oddities

Numbers can be extremely misleading: `[0x_for x in (1, 2, 3)]` evaluates to `[15]`. It startles enough people that Python 3.10 has deprecated this kind of syntax misuse.

<details> <summary> <i> Expand for an explanation. </i> </summary> This means <code>[0xF or (x in (1, 2, 3))]</code>. <code>0x_f</code> is a hexadecimal integer literal, and F in hex equals 15 as a decimal. <code>o</code> isn't a hex digit, so Python doesn't lump <code>or</code> into the literal. (<code>15</code> is truthy, so using Boolean short circuiting, the <code>x in (1, 2, 3)</code> is never executed. That means <code>x</code> being undefined causes no error. The <code>15</code> is in a list literal rather than a list comprehension.) </details>

#### Quasi-operators
Whitespace can be a very confusing tool for things other than numbers. For instance, the so called 'pistol operator' or 'comma assignment' —
```python
numbers = [42]
only_element ,= numbers
print(only_element) # 42
```
— is better rendered as `only_element, = numbers`, `(only_element,) = numbers`, or equivalently `[only_element] = numbers`. The last makes it clear that the pistol operator uses iterable unpacking and irresponsible whitespace to extract the only element of a list. The 'space-invader increment operator' `x -=- 1` is in a similar vein (parsing as `x -= -1`).

## Semicolons
Semicolons can be useful to avoid needing to type indentation characters; they act as a replacement newline for separating statements because Python and C are basically the same language.
```python
while 2+2!=5: print("Try again."); break;
```
Both statements are part of the loop.

## Practicalities
—Whitespace is great, but what does it mean for golfing?

I'm glad you asked. If you have only one indentation level, put it all on the same line. `for x in y:a;b;c`

If you have more than one of them, the outer indentation levels will need their own line. You don't need to use four spaces for each indent. A single space (or tab, you heathen!) will suffice.
```python
if-1>a:
 a
 b
 while c:
  try:d
  except:0
```
