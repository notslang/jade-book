#Basic Syntax
Now that you know what Jade actually is, let's enter that part of the book where you start to learn how to write Jade.

##Significance of whitespace
Rather than using opening/closing tags to delimit the start/end of blocks, Jade uses indentation. This can be a little strange if you're used to languages where white space doesn't matter (such as JS, CSS, and of course HTML). However, it does have the benefit of forcing you to have good indentation, preventing horrible formatting as illustrated in the following code (which is a perfectly valid HTML):

```html
<!DOCTYPE html>
<html><head><title>An Example</title></head>
<body><h1>Horrible Formatting</h1>
<p>Never write HTML like this, it is <i>really</i> hard to read</p>
</body></html>
```

Also, white space significance prevents stupid errors like this:

```html
<i>notice the order of the <b>closing tags</i></b>
```

Now onto how it actually works:

![](img/Indentation.svg)

Each level of indentation (the rectangles outlined with dashed lines) creates a new block (the pastel-colored sections) out of each following line that is indented to that level. Each of those blocks becomes a child of the tag on the line that appears immediately before the block (notice the tags with colors that correspond to their blocks).

------------------------
**ProTip**
Most editors let you manipulate entire blocks of code by selecting several lines and then pressing Tab to indent the block or Shift + Tab to "de-dent" it. This is very useful when working in a whitespace-significant language like Jade.

------------------------

##Tags
Since Jade is indentation-based, there are no end tags, and there are no `<`s or `>`s to surround tags, because those are lame and ugly. The name of the tag is all that you need, and it is the first text on the line. Consider the following example:

```jade
p
```

```html
<p></p>
```

If we add another tag within the `<p>` block (as explained earlier), we can create a nested tag as follows:

```jade
p
  span
```

```html
<p>
  <span></span>
</p>
```

Alternatively, without putting it in the `<p>` block, we can just create it in a way that it acts as a sibling:

```jade
p
span
```

```html
<p></p>
<span></span>
```

###Text and blocks of text
Tags are pretty boring if they don't have any content, so Jade gives us three ways of putting text in tags.

###Text on the same line
You can put the text directly after the tag name (separated by a space) as follows:

```jade
p Hello Word!
```

```html
<p>Hello Word!</p>
```

###Text blocks
For large bodies of text, putting it on the same line isn't very practical, so we have text blocks. These are indicated by a leading `|` (pipe) character.

```jade
p
  | This is a demonstration
  | of Jade's text blocks
```
```html
<p>
  This is a demonstration
  of Jade's text blocks
</p>
```

These text blocks can be mixed with regular tags as follows:

```jade
p
  | This is a demonstration
  br
  | of Jade's text blocks
```

```html
<p>
  This is a demonstration
  <br>
  of Jade's text blocks
</p>
```

###Shorthand text blocks
If all you have is a big block of text or code, adding all those pipe characters can be a pain. So Jade provides a shorthand method for indicating that all the nested code in an element are text blocks. This is represented by a `.` after the tag as follows:

```jade
p.
  This is a demonstration
  of Jade's text blocks,
  using the "." shorthand
```

```html
<p>
  This is a demonstration
  of Jade's text blocks,
  using the "." shorthand
</p>
```

##In-line HTML
It's also perfectly fine to put in-line HTML in any of those text blocks as shown in the following example:

```jade
p.
  This is a <i>
  demonstration</i>
  of Jade's <b>text
  blocks</b>
```

```html
<p>
  This is a <i>
  demonstration</i>
  of Jade's <b>text
  blocks</b>
</p>
```

##Attributes
Attributes are also pretty important, so here's how to write those:

```jade
p(id="hello") Hello Word!
```

```html
<p id="hello">Hello Word!</p>
```

That's right! They're pretty similar to the way you write attributes in HTML, except they're surrounded by a pair of parentheses. Also, if you have multiple attributes, they're delimited by commas, rather than just spaces. An example of this is as follows:

```jade
p(id="hello", class="world")
```

```html
<p id="hello" class="world">
</p>
```

------------------------
**ProTip**
Jade `0.35.0` (released 2013-08-21) added support for space-separated attributes. Soon, this will be supported by syntax highlighters, syntax checkers, and related tools like `html2jade` - but until then you may wish to stick with the comment-delimited syntax. For this reason, the rest of this book will use comment-delimited attributes.

------------------------

###Passing Objects as Attributes
Not only can you pass strings as attributes, but if you pass objects they will be turned into the most useful representation for that particular attribute. For example, passing an array to the `class` attribute will be interpreted as a list of classes:

```jade
p(class=['first-class', 'another-class', 'last-class'])
```

```html
<p class="first-class another-class last-class"></p>
```

As you can see, it results in a valid, space-delimited list of classes. Another example is when you pass any type of object to a `data-*` attribute, it will be encoded as JSON:

```jade
p(data-myattr={numbers: [2, 4, 8], string: 'this is a string'})
```

```html
<p data-myattr='{"numbers":[2,4,8],"string":"this is a string"}'></p>
```

However, for most attributes it just outputs the standard string representation of the object:

```jade
p(value=['one', 'two', 'three'])
```

```html
<p value="one,two,three"></p>
```

This isn't incredibly useful unless the object that you're passing has a custom `.toString()`.

##Shorthands

###IDs and classes
IDs and classes are both pretty common attributes, so Jade gives us a shorthand method for writing them. This is similar to the way CSS selectors are written. An example of this is as follows:

```jade
p#hello Hello Word!
```

```html
<p id="hello">Hello Word!</p>
```

```jade
p#hello.world
```

```html
<p id="hello" class="world">
</p>
```

Pretty familiar, eh? IDs are just prefixed with a `#` (pound symbol) and classes are prefixed with `.` (a peroid). These may be put in any order after the tag name with any number of classes.

###Automatic div
Because the `div` tags are used so frequently, Jade offers a shorthand way for writing them - by omitting the tag, Jade assumes you want to use a `div` tag; therefore, the following code:

```jade
div#hello Hello Word!
```

```html
<div id="hello">
Hello Word!
</div>
```

It can also be rewritten as:

```jade
#hello Hello Word!
```

```html
<div id="hello">
Hello Word!
</div>
```

However, this is possible only as long as there is an ID and/or class(es) where the tag name would normally be.

##Comments

###Single line
Normal HTML comments are pretty verbose, so Jade offers us a much shorter way to write them that looks similar to JavaScript comments.

```jade
//a single line comment
```

```html
<!-- a single line comment-->
```

Also, if you don't want your comments to show up in the compiled HTML, you can use silent comments by adding a `-` (hyphen) after `//`.

```jade
//- a silent single line comment
```

###Block comments
But of course, we need to be able to comment out multiple lines too; for that, we use block comments. If you indent a block after a comment, that block will be added to the comment too. An example of this is as follows:

```jade
// a block comment
  h1 Now I'm Commented Out.
  p And me too.
```

```html
<!-- a block comment
<h1>Now I'm Commented Out.</h1>
<p>And me too.</p>-->
```

As you can see, the first line of the comment becomes a text block, and the indented block is parsed normally. However, the first line is entirely optional and is generally just used to note what was commented out. We can omit it if we want to:

```jade
//
  h1 Now I'm Commented Out.
  p And me too.
```

```html
<!--
<h1>Now I'm Commented Out.</h1>
<p>And me too.</p>-->
```

And of course, silent comments work here too:

```jade
//-
  h1 Now I'm Commented Out.
  p And me too.
```

But what's really cool about block comments is that they automatically detect when you're trying to make a conditional comment for IE.

```jade
// if lt IE 9
  script(src='/ie.js')
```

```html
<!--[if lt IE 9]>
<script src="/ie.js"></script>
<![endif]-->
```

##Block expansion
When each tag only has one tag nested under it, it can be a little annoying to have a new line for each one of them:

```jade
ul
  li.first
    a(href='#') foo
  li
    a(href='#') bar
  li.last
    a(href='#') baz
```

So, Jade has a feature called block expansion that lets us put those tags on the same line, meaning we can rewrite the above example as this:

```jade
ul
  li.first: a(href='#') foo
  li: a(href='#') bar
  li.last: a(href='#') baz
```

The `:` (colon) after the tag name and attributes indicates that there is another tag following that one. We can even make really long chains of tags:

```jade
ul: li.first: b: a(href='#') foo
```

```html
<ul>
  <li class="first">
    <b>
      <a href="#">foo</a>
    </b>
  </li>
</ul>
```

But that is really hard to read, so please, *never* do that unless you have a very good reason.

##Doctypes
Doctypes can be really long, so naturally Jade gives us a much shorter way to write them.

```jade
doctype
```

```html
<!DOCTYPE html>
```

```jade
doctype default
```

```html
<!DOCTYPE html>
```

```jade
doctype html
```

```html
<!DOCTYPE html>
```

```jade
doctype xml
```

```html
<?xml version="1.0" encoding="utf-8" ?>
```

```jade
doctype transitional
```

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

```jade
doctype strict
```

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
```

```jade
doctype frameset
```

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
```

```jade
doctype 1.1
```

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
```

```jade
doctype basic
```

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML Basic 1.1//EN" "http://www.w3.org/TR/xhtml-basic/xhtml-basic11.dtd">
```

```jade
doctype mobile
```

```html
<!DOCTYPE html PUBLIC "-//WAPFORUM//DTD XHTML Mobile 1.2//EN" "http://www.openmobilealliance.org/tech/DTD/xhtml-mobile12.dtd">
```

You can also write your own doctype as follows:

```jade
doctype html PUBLIC "-//W3C//DTD XHTML Basic 1.1//EN"
```

```html
<!DOCTYPE html public "-//w3c//dtd xhtml basic 1.1//en">
```

------------------------
**ProTip**
You may notice `!!!` used instead of `doctype` in old Jade code. This used to be a standard shorthand for doctypes, but `!!!` is now deprecated because it isn't expressive enough.

------------------------

##Summary
In this chapter, we dived into the language itself, covering the very basics of the syntax. This included how indentation-based syntaxes work and how to write tags, text, attributes, comments, and some nifty shorthands for classes, IDs, and doctypes.
