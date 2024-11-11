---
title: String Formatting in JavaScript
date: 2022-03-13T13:08:29-07:00
categories: [Coding, Tips & Tricks]
tags: [JavaScript, Python, Strings]
---

> TL;DR...basically `.format()` for JavaScript

These days, when developers need to piece strings together or represent non-string
values in string format, most reach immediately for [string interpolation][1] when
it's available. It makes sense---most of the time, string interpolation is
not only the best approach, it's also the most readable. That said, string interpolation
can feel a little restrictive when you realize that the interpolation can only happen
once: at the point of initialization.

You might be wondering: when would you need the interpolation to happen at a time
_other than_ when the string is initialized? The answer, in my experience, is rarely.
However, when you _do_ need it, it's really frustrating when the programming language
you're using _\*cough\* JavaScript \*cough\*_ doesn't support it out of the box.

## Python Example

A great way to demonstrate this restriction is to take a look at Python's [f-strings][2]
(introduced in Python 3.6) and compare them with how string formatting was done prior
to their addition to the language.

For those unfamiliar with f-strings, they allow string interpolation with the following
syntax:

```python
name = "Bob"
origin = "Canada"
greeting = f"Hello, {name} from {origin}!"

print(greeting)     # prints "Hello, Bob from Canada!"
```

Before the advent of f-strings, Pythonistas had to choose from a few different (and less readable) options, such as:

```python
# 1. string concatenation
greeting = "Hello, " + name + " from " + origin + "!"

# 2. C-style string formatting
greeting = "Hello, %s from %s!" % (name, origin)

# 3. The str.format() method (shown here with indexes)
greeting = "Hello, {0} from {1}!".format(name, origin)
```

One of the benefits of the latter two approaches, however, was that you could define
your string with placeholders beforehand, and then perform the formatting later with
whatever values you wanted to:

```python
# Define your greeting string:
greeting = "Hello, {0} from {1}!"

# Set some variables
name = "Bob"
origin = "Canada"

# Format your string
print(greeting.format(name, origin)) # prints "Hello, Bob from Canada!"

# Change the variable values
name = "Sally"
origin = "Kansas"

# Use the same string and format with new values
print(greeting.format(name, origin)) # prints "Hello, Sally from Kansas!"
```

Trying to run something similar with classic string interpolation of course doesn't
work:

```python
# Running this:
greeting = f"Hello, {name} from {origin}!"

name = "Bob"
origin = "Canada"

# Results in this:
```

```pseudo
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'name' is not defined
```

## Building `.format()` in JavaScript

So what do you do when you want functionality similar to Python's [`str.format()`][3]
method in JavaScript?

You build it yourself.

There are a number of different approaches to take here---including [hacking directly
into the `String` prototype][4]---but personally, I find the most interesting (and
mind-bending) approach to use the ES6 feature of template strings called [tagged
templates][5].

### What are tagged templates?

If you've never heard of tagged templates, you're in good company. They're far from
the most commonly used ES6 feature, in part because they can be difficult to wrap
your head around. At their core, tagged templates are just template literals,
parsed by a function.

The syntax looks like this:

```js
const taggedTemplate = myTag`Some template literal here`;
```

In the example above, the `myTag` function receives one argument: an array containing
the string from the template literal. The `taggedTemplate` variable is then set to
whatever value the `myTag` function returns.

What's special about the tagged template syntax, however, is that if the template
literal contains any interpolation-like syntax (e.g., `${name}`), the `myTag` function
will instead receive multiple arguments: an array of the string, split at every instance
of `${}`, followed by an argument for every "key" provided within the curly braces.

For example, in the tagged template below...

```js
myTag`This is test number ${'num'} on day ${'day'}.`
```

...the `myTag` function will be called like so:

```js
myTag(['This is test number ', ' on day ', '.'], 'num', 'day')
```

### Using tagged templates to make a dynamic formatter

Knowing this, we can make a tagged template that acts similar to the `str.format()`
method. For somewhat basic functionality, we know that we need the following:

1. Our tag function should work with template literals that use any number of placeholder values.
2. If given more than one value, we should have a way to specify which value is used with which placeholder.
3. Formatting should be invoked at any time, like a function call.
4. Ultimately, we should return a string after formatting has been invoked.

#### Build from the outside-in

To start, we know that no matter what, tag functions will always be passed an array
of strings as the first argument. So our function can begin with something like this:

```js
function format(strings) {
  // do something
}
```

We also know that the rest of the arguments will be the placeholders from the template
literal. To capture all of these in an array for later use, we will update our function with the
ES6 `...` operator:

```js
function format(strings, ...keys) {
  // do something
}
```

At this point, we can test our `format` tag and print out `strings` and `keys` to
make sure we know what is going on.

```js
function format(strings, ...keys) {
  console.log("Strings", strings);
  console.log("Keys", keys);
}

const test = format`Hello ${'name'}! This is test ${1}.`
```

In the terminal we see the output of our `console.log()` calls:

```pseudo
Strings [ 'Hello ', '! This is test ', '.' ]
Keys [ 'name', 1 ]
```

#### Make use of placeholders

To achieve goal #2 (specifying which value to use in each part of the string) we
will have to utilize our placeholder keys. Since numeric keys come through as numbers,
it's clear that implementing an index-based placeholder system will be fairly straightforward,
assuming we pass in an array of values:

- Map over each string, keeping track of the index
- To each string, add the value at the index specified by the key (if the key is `2`, add the value at index 2 to the string)
  - Note: the index of the key will be the same index as the current string
- Join all strings in the array and return the resulting string.

Roughly, we might want it to function something like this:

```js
const values = ["world", 2];

function format(strings, ...keys) {
  return strings
    .map((str, i) => `${str}${values[keys[i]]}`)
    .join('');
}

const test = format`Hello ${0}! This is test ${1}.`;
console.log(test); // prints "Hello world! This is test 2.undefined"
```

Almost! We forgot to account for when our `strings` array is longer than our `keys`
array.  In the above example, the final iteration of our map tries to access
`values[keys[2]]`, which is undefined. A simple fix brings us to a working formatter:

```js {hl_lines=[5]}
const values = ["world", 2];

function format(strings, ...keys) {
  return strings
    .map((str, i) => `${str}${values[keys[i]] || ''}`)
    .join('');
}

const test = format`Hello ${0}! This is test ${1}.`;
console.log(test); // prints "Hello world! This is test 2."
```

#### Convert to closure

Thus far we've managed to build a tagged template that meets goals 1, 2 and 4,
but we still had to define our values before initializing it as `test`. So how
can we change this to allow for passing in a set of values at any time?

We need to convert our tagged template from something that returns a string to
something that returns a function.

> Note: The following pattern will look familiar to some of you, but will be completely
foreign to others. If you haven't heard of closures in JavaScript or need to brush
up on them again, I highly recommend the [Scopes and Closures chapter][6] in Kyle Simpson's
book series, [_You Don't Know JS Yet_][7].

Syntactically, this is a trivial change, but it's important to take a second to
fully understand what's going on in the resulting code:

```js {hl_lines=[2, 8]}
function format(strings, ...keys) {
  return (...values) => strings
    .map((str, i) => `${str}${values[keys[i]] || ''}`)
    .join('');
}

const greetingTest = format`Hello ${0}! This is test ${1}.`;
console.log(greetingTest('world', 3));
// prints "Hello world! This is test 3."
```

Here we are assigning the return value of our tagged template to the name `greetingTest`.

The tagged template returns a function that: a) accepts any number of values; and b) returns
a string composed of the strings in our template literal and the values passed to the function.

The values are inserted into the resulting string based on the index specified by
the placeholder keys in the template literal.

And finally, the values given to `greetingTest` do not need to be predefined. They
can be provided on the fly.

Of course, we don't need to assign the tagged template to a variable or constant to invoke it.
The last two lines could be swapped out for this:

```js
console.log(format`Hello ${0}! This is test ${1}.`('world', 3))
```

## Final Code

After a bit of work, we have a perfectly functional equivalent to Python's
`.format` in just a few lines of JavaScript:

```js
function format(strings, ...keys) {
  return (...vals) => strings
    .map((str, i) => `${str}${vals[keys[i]] || ''}`)
    .join('');
}
```

One noticeable difference from Python's `.format()` is that in Python we can create
an actual _string_ variable with placeholders in it and call the `.format()` method on
that variable later.

With our solution, we instead save the resulting _function_ of the tagged template string,
and invoke that function directly.

But in the end, the usage isn't all that different between the two implementations:

### Python

```python
greeting = "Greetings from the year {1}, {0}!"
name = "Human"
year = 2222

# Greetings from the year 2222, Human!
print(greeting.format(name, year))
```

### JavaScript

```js
// `format` defined previously
const greeting = format`Greetings from the year ${1}, ${0}!`;
const name = "Human";
const year = 2222;

// Greetings from the year 2222, Human!
console.log(greeting(name, year));
```

While it's not quite as powerful as its Python counterpart, it accomplishes what we set out to do.

If you're interested, you can try it out using the REPL below, and you can even try
implementing keywords by following [the second example][5] in the Mozilla documentation.

<details>
<summary><b>Click here to try it live in a REPL</b></summary>
<iframe frameborder="0" width="100%" height="500px" src="https://replit.com/@elCocodrilo/CleverLiveStrings?embed=true"></iframe>
</details>

[1]: https://en.wikipedia.org/wiki/String_interpolation
[2]: https://docs.python.org/3/tutorial/inputoutput.html#tut-f-strings
[3]: https://docs.python.org/3/tutorial/inputoutput.html#the-string-format-method
[4]: https://stackoverflow.com/questions/610406/javascript-equivalent-to-printf-string-format
[5]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates
[6]: https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/scope-closures/ch7.md
[7]: https://github.com/getify/You-Dont-Know-JS
