# Logic in templates
Ok, now that we know how to send data to templates and display them in the
resulting HTML, we can actually make useful templates. However, sometimes we
need a little more power. For that, we turn to logical operations.

## Adding logic with JavaScript
As I've already mentioned, Jade compiles into JS and allows you to use JS
directly in your template. So, we can use any of the logical operators that JS
provides to build our markup.

### If / else
The most basic logical operator is the if statement:

```jade
- name = "Bob"

- if (name == "Bob") {
  h1 Hello Bob
- } else {
  h1 My name is #{name}
- }
```

```html
<h1>Hello Bob</h1>
```

And the shorthand form (the ternary operator) works too:

```jade
- name = 'Bob'
- greeting = (name == 'Bob' ? 'Hello' : 'My name is')
h1 #{greeting} #{name}
```

```html
<h1>Hello Bob</h1>
```

------------------------
**ProTip** Switches don't work. Use the `case` statement that is explained in
the next section.

------------------------


### For loops
Loops can be used to iterate over lists, or repeat elements a certain number
of times.

```jade
- list = ['one', 'two', 'three'];

ul
  - for (var i = 0; i < list.length; i++){
      li=list[i]
  - }
```

```html
<ul>
  <li>one</li>
  <li>two</li>
  <li>three</li>
</ul>
```

### Complex operations
Generally, you should keep complex operations outside of your templates, but
it's worth noting that you can do pretty extensive data manipulation.

```jade
- String.prototype.title_case = function() {
-   return this.replace(/\w\S*/g, function(txt){
-     return txt.charAt(0).toUpperCase() + txt.substr(1).toLowerCase();
-   });
- };

p="this is a title".title_case()
```

```html
<p>This Is A Title</p>
```


## Built-in logical operators
Writing JS can start to look ugly and doesn't really match with the
indentation-based syntax that Jade uses, so we have several built-in logical
operators that do the same thing, but are easier to write.

Here are a few examples:

### If / else / else if
```jade
- name = "Bob"
if name == "Bob"
  h1 Hello Bob
else if name == "Joe"
  h1 Hello Joe
else
  h1 My name is #{name}
```

```html
<h1>Hello Bob</h1>
```

### Unless
Jade also provides `unless` which is equivalent to `if (!(expr))`

```jade
- name = "Bob"
unless name == "Bob"
  h1 My name is #{name}
else
  h1 Hello Bob
```

```html
<h1>Hello Bob</h1>
```

### Cases
```jade
- name = "Bob"
case name
  when "Bob"
    p Hi Bob!
  when "Alice"
    p Howdy Alice!
  default
    p Hello #{name}!
```

```html
<p>Hi Bob!</p>
```

### Each loops
Each is used for iterating over arrays and objects, and is written in the
form: `each VAL[, KEY] in OBJ`

```jade
- list = ["one","two", 'three']

ul
  each item in list
    li=item
```

```html
<ul>
  <li>one</li>
  <li>two</li>
  <li>three</li>
</ul>
```

```jade
- books = ["A", "B", "C"]

select
  each book, i in books
    option(value=i) Book #{book}
```

```html
<select>
  <option value="0">Book A</option>
  <option value="1">Book B</option>
  <option value="2">Book C</option>
</select>
```

```jade
- books = {"000":"A", "001":"B", "010":"C"}

select
  each book, i in books
    option(value=i) Book #{book}
```

```html
<select>
  <option value="000">Book A</option>
  <option value="001">Book B</option>
  <option value="010">Book C</option>
</select>
```

------------------------
**ProTip** You can also use `for` in place of `each` - they mean the same
thing

------------------------

### While loops
```jade
- list = ["one","two", 'three']
- i = 0

ul
  while i < list.length
    li=list[i]
    - i++;
```

```html
<ul>
  <li>one</li>
  <li>two</li>
  <li>three</li>
</ul>
```

## A warning about interpolation
Interpolation cannot be used in code blocks, including vanilla JS and the
builtin variants. For example:

```jade
- var there = "foo"
- title = "Hello #{there}"
p #{title}
```

```html
<p>Hello #{there}</p>
```

If interpolation worked in code, then it would print out `Hello foo`. The
reason why interpolation can't be used in code blocks is because vanilla JS
has no interpolation, and very little processing is done to the code before it
is executed (even the built in shorthands). So allowing interpolation in these
places would require extensive re-writing of the code blocks during
compilation. However, once Template Strings are implemented in ES6 (the next
version of JavaScript) this won't be an issue.
