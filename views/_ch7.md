#Template Inheritance

##What It's Used For
Sites generally have a basic layout which is the same across all pages, and then small "blocks" of HTML that make each page unique. To prevent you from needing to repeat this base layout in every single file, Jade uses a block system that lets you insert interchangeable blocks into templates.

##Blocks
Blocks function like small containers for Jade. Their content can be appended to, prepended to, or replaced entirely. To define a block, simply use the `block` keyword, and then the name of the block:

```jade
block scripts
  script(src='jquery.js')
```

```html
<script src="jquery.js"></script>
```

By default, a block will just output the nested content, but blocks really become useful when you start to extend them. Blocks can also be nested inside other tags, making them useful as placeholders. For example, in the following file (which we will use for examples throughout the rest of the chapter), we will define 3 blocks; `scripts`, `styles`, and `content`.

*layout.jade*

```jade
!!!
html
  head
    block scripts
      script(src='jquery.js')
    block styles
  body
    block content
      p there's no content here
```

```html
<!DOCTYPE html>
<html>
  <head>
    <script src="jquery.js"></script>
  </head>
  <body>
    <p>there's no content here</p>
  </body>
</html>
```

By default, the only script that's on the page is jQuery; there are no styles, and in the body there is a short message explaining that there's no content on the page. That's pretty boring, so next we're going to learn how to extend this page to make it better.

###Blocks Don't Provide Encapsulation
Variables defined in blocks can be accessed outside of blocks. For example:

```jade
block example
  variable_from_a_block = 'I was defined inside a block'

p=variable_from_a_block
```

```html
<p>I was defined inside a block</p>
```

I would not recommend accessing variables defined in blocks outside of blocks, since replacing the content of the block defining that variable would remove the variable and break whatever it was used in. Also, it makes more sense logically to group the variables with the place they are used, when possible.

But even if you follow this recommendation, it is still important to note that they are in the same name-space, so reusing a variable name redefines it.

##Extends
The extends keyword allows us to specify that a particular template extends another template. This means the template in which the keyword is used gets to modify the blocks of the other template.

The syntax is simple; using `extends layout` means that the template in which it is used gets to extend `layout.jade` (the `.jade` part of the filename is implied). Also, full paths can be used; like if `layout.jade` was 1 directory above the location of the current template, we could use `../layout` to access it.

###Replace
To replace the content of a block, we use the same syntax as defining a block, but it must be put in a template that extends the file in which the block was defined. For example, if we have a page in which we need both jQuery and underscore.js, we could redefine the `scripts` block as follows:

*page1.jade (in the same directory as layout.jade)*

```jade
extends layout

block scripts
  script(src='jquery.js')
  script(src='underscore.js')

block content
```

```html
<!DOCTYPE html>
<html>
  <head>
    <script src="jquery.js"></script>
    <script src="underscore.js"></script>
  </head>
  <body>
  </body>
</html>
```

I also redefined the `content` block to be blank, because you don't necessarily need to pass new content.

###Append

In the previous section, we completely redefined the script block, even though we were really just adding to it. We could simplify this example by using the `append` keyword.

*page2.jade (in the same directory as layout.jade)*

```jade
extends layout

append scripts
  script(src='underscore.js')

block content
```

```html
<!DOCTYPE html>
<html>
  <head>
    <script src="jquery.js"></script>
    <script src="underscore.js"></script>
  </head>
  <body></body>
</html>
```

`page2.jade` results in the same HTML as `page1.jade`, But 

Depending on your preference, you could decide to write `append block` rather than just `append`. They mean the same thing, but `append` is shorter, so that will be used in all examples throughout this book.

###Prepend

The `prepend` keyword does the exact opposite of the append keyword, and also has a longer variant: `prepend block`. Prepend is useful when you want to add something to the beginning of a block. Like if you want underscore.js to load before jQuery, you could do the following:

*page2.jade (in the same directory as layout.jade)*

```jade
extends layout

prepend scripts
  script(src='underscore.js')

block content
```

```html
<!DOCTYPE html>
<html>
  <head>
    <script src="underscore.js"></script>
    <script src="jquery.js"></script>
  </head>
  <body></body>
</html>
```

And, as you can see, the order is switched.

###Incompatibility
It is worth noting that blocks are evaluated during compilation, so they will not work with render-time logic like if/else statements. For example, the following will break:

```jade
if true
  extends layout1
else
  extends layout2
```

This is a rather "edge" case because there is usually no reason to structure your templates in such a way that render-time logic influences compile-time statements. Thus, this incompatibility will probably not be fixed.

###Extra Things in Extenders
If you have things, other than blocks, in a template which extends another template, they will be ignored. For example:

*minimal_layout.jade*

```jade
p=a_variable
```

*ignored_things.jade (in the same directory as minimal_layout.jade)*

```jade
extends minimal_layout
a_variable = 'I won\'t show up'
p I won't show up either
```

```html
<p></p>
```

As you can see, `a_variable` cannot be accessed in `minimal_layout.jade` because it was defined in a template extending it. Similarly, the `p` tag from `ignored_things.jade` doesn't show up because markup is ignored in extending templates.

##Includes
The last way to insert content from another file is with an `include` statement. This is the simplest way, but also the least dynamic because you cannot change/generate the name of the file you want to include. This is because includes are one of the first things that are evaluated when compiling a template; before any loops, logical operations, or variables.

Still, they are quite useful for moving pieces of templates that are reused many times into their own files, or for including static assets like HTML, CSS, or JS directly in templates.

###Static
If you just want to include a static asset, the operation is very basic.

*style.css*

```css
p {
  color: blue;
  text-decoration: underline;
}
```

*content.html (in the same directory as style.css)*

```html
<h1>includes</h1>
<p>this is a file for demonstrating the use of includes in Jade</p>
```

*example.jade (in the same directory as style.css)*

```jade
!!!
html
  head
    include style.css
  body
    include content.html
```

```html
<!DOCTYPE html>
<html>
  <head>
<style type="text/css">
p {
  color: blue;
  text-decoration: underline;
}
</style>
  </head>
  <body>
<h1>includes</h1>
<p>this is a file for demonstrating the use of includes in Jade</p>
  </body>
</html>
```

In the case of the CSS, it is automatically wrapped in `<style>` tags (just as JS is wrapped with `<script>` tags), but the contents of the HTML file are passed through in verbatim.

###Filtered

If you try to include a Markdown, Stylus, CoffeeScript, or any of the other types of files mentioned in chapter 3, Jade will automatically compile it for you. For example, if you use `include file.md` then `file.md` will be compiled as Markdown and the resulting HTML will be injected into the template.

For example:

*style.styl*

```stylus
p
  color blue
  text-decoration underline
```

*content.md (in the same directory as style.styl)*

```markdown
#includes
this is a file for demonstrating the use of includes in Jade
```

*filters.jade (in the same directory as style.styl)*

```jade
!!!
html
  head
    include style.styl
  body
    include content.markdown
```

```html
<!DOCTYPE html>
<html>
  <head>
<style type="text/css">
p {
  color: #00f;
  text-decoration: underline;
}
</style>
  </head>
  <body>
<h1>includes</h1>
<p>this is a file for demonstrating the use of includes in Jade</p>
  </body>
</html>
```

...And you can see that both of the included files are compiled and inserted into the file.

###Jade

If you are including a Jade file, then rather than compiling it into HTML, it will be parsed and the AST (Abstract Syntax Tree) will be injected into the spot where the include was. This means that even variables behave as if they were written in the same file. Also, if you're including a Jade file, you don't need to use the `.jade` file extension.

*book-format.jade*

```jade
p #{book.title} by #{book.author}
```

*example2.jade (in the same directory as book-format.jade)*

```jade
books = [{title: "Godel Escher Bach: An Eternal Golden Braid", author: "Douglas Hofstadter"}, {title: "Slaughter-House Five", author: "Kurt Vonnegut"}];

for book in books
  include book-format
```

```html
<p>Godel Escher Bach: An Eternal Golden Braid by Douglas Hofstadter</p>
<p>Slaughter-House Five by Kurt Vonnegut</p>
```

And, as you can see, book.title is available even though it is accessed in code that was written in `book-format.jade`.

##A Note About Variable Scope

As we proved in the last section, when Jade is simply `include`ed all the top-level variables from the file that we inserted are available in the scope that the include statement was placed in. However, blocks do not behave this way - they have encapsulation.

For example:

....

##Summary

In this chapter we learned about the last part major of the language: template inheritance, which is done through a block system. In addition, we learned about `include`, a related keyword that lets us include non-Jade files, like scripts and styles.
