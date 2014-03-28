# Mixins
Mixins are small, encapsulated pieces of code that are reusable throughout the
template. They allow you to reduce redundancy (repeating chunks of code) and
can make code easier to understand by providing good names for your mixins
(more about this in "Organizing Jade Projects"). Because they are
encapsulated, they have their own variable scope, meaning they can prevent
naming collisions that would likely happen in large templates that only use
Jade's "global" namespace.

They are very similar to functions in JS - in fact they compile into slightly
modified functions and corresponding function calls. This means that almost
everything that you already know about functions in JS carries over to
"mixins" in Jade.

## Syntax & mechanics
First off, we're going to talk about how to write mixins; the syntax that they
use, and what it does. Also, since we've already covered logical operations in
templates, we can use those in mixins throughout the examples.

### Defining mixins
Mixins definitions don't output any HTML and are defined using the following
syntax:

```jade
mixin book(name, price)
  li #{name} for #{price} €
```

where `book` is the name of the mixin, and `name` & `price` are both named
arguments. The indented block of Jade gets executed in its own scope, where
the variables `name` and `price` are both defined with the args that are
passed. So basically: it works just like you would expect a function to work.

### Calling mixins
The syntax for calling mixins is also similar to that of function calls,
except we prefix the function name with a `+` to say that it isn't a tag
(which can look quite similar). So using our `book` mixin, we can call it with
the following:

```jade
ul#books
  +book("Book A", 12.99)
  +book("Book B", 5.99)
```

```html
<ul id="books">
  <li>Book A for 12.99 €</li>
  <li>Book B for 5.99 €</li>
</ul>
```

Simpler mixins don't even need to accept arguments, and can be called without
args too:

```jade
mixin copyleft
  | (&#596;)

p
  +copyleft
  | - Sean Lang - 2013
```

```html
<p>(&#596;) - Sean Lang - 2013</p>
```

### Passing blocks
Besides just being able to pass arguments, you can also pass entire blocks to
a mixin:

```jade
mixin input(name)
  li(id=name.replace(/\s/g, '-'))
    label= name + ':'
    block

form: ul
  +input('favorite color')
    input('type'='text')
  +input('comments')
    textarea Type your comment here.
```

```html
<form>
  <ul>
    <li id="favorite-color">
      <label>favorite color:</label>
      <input type="text">
    </li>
    <li id="comments">
      <label>comments:</label>
      <textarea>Type your comment here.</textarea>
    </li>
  </ul>
</form>
```

The block that will be passed to the mixin is whatever indented block comes
after the mixin call. It is just like the way in which whatever indented block
comes after a tag is nested in that tag, except here they are passed to the
mixin. In this case, `input('type'='text')` and `textarea Type your comment
here.` are the passed blocks. Inside the mixin, the `block` keyword tells Jade
where to put the contents of the block that is passed to it.

At the same time, the mixin also takes the `name` arg which is used to make
the id and label.

## Another warning about interpolation
Back in chapter 3 I mentioned that interpolation doesn't work in the arguments
used to call a mixin. Now that we know how to write mixins we need to be
careful not to use interpolation when we're calling them. For example:

```jade
mixin hello(p)
  | #{p}

- title = "This is my Title"
p
  mixin hello('#{title}')
```

```html
<p>#{title}</p>
```

If interpolation did work in mixin arguments then this would output `This is
my Title` rather than `#{title}`.

This gotcha has existed since Jade's creation, and has been discussed on
multiple occasions (see: https://github.com/visionmedia/jade/issues/693) but
probably won't be changed any time soon.

## Arguments object
Just as the `arguments` object is a local variable available in JavaScript
objects, it is available in Jade mixins. In fact, it is used frequently in
Jade to make mixins that accept a variable number of args.

```jade
mixin list()
  ul
    - var args = Array.prototype.slice.call(arguments);
    for item in args
      li= item

+list('one', 'two', 'three')
```

```html
<ul>
  <li>one</li>
  <li>two</li>
  <li>three</li>
</ul>
```

In the above example, we define a mixin (`list`) that appears to take no
arguments, but in fact iterates over an array created from the `arguments`
object. It is worth noting that we cannot iterate over `arguments` itself,
because it is **not a real array**. Instead, we use `- var args =
Array.prototype.slice.call(arguments);` to make an array called `args` from
the `arguments` object.
