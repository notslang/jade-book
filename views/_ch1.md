#What is Jade?
Jade is a templating language and a shorter, more elegant way to write HTML. If you are just looking for a good way to create templates, or you want to ditch HTML's ugly syntax, Jade can help you.

##Markup like poetry
Let's start with a simple example. First we have HTML, and then the same thing rewritten in Jade. 

```html
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>
    <h1>Meet Jade</h1>
    <p>
      A simple Jade example.
      You'll learn to write
      all of this in ch 2.
    </p>
    <p>Jade FTW!</p>
  </body>
</html>
```

```jade
!!!5
html
  head
  body
    h1 Meet Jade
    p.
      A simple Jade example.
      You'll learn to write
      all of this in ch 2.
    p Jade FTW!
```

Both of the preceding code examples mean the exact same thing, except one is much shorter. This is Jade, a powerful, terse templating language that is compiled into HTML. In addition to the syntactical improvements, Jade lets you simplify redundant markup with programmed logic. Also, it allows you create templates which can take in, and display data.

##Why Should I Preprocess?
Jade really is just one option in a whole class of preprocessors; to have a complete understanding of Jade, we should understand why this class of languages was created.

Preprocessors are high-level languages that offer syntactical and functional improvements over their "vanilla" (non-preprocessed) counterparts. These high-level languages allow you to write the markup in a better language that is compiled down to normal (vanilla) HTML. Thus, they are there purely to improve your productivity, without affecting their compatibility with existing technologies.

Preprocessing, in general, offers many benefits over writing vanilla HTML. SGML (Standard Generalized Markup Language), the predecessor of HTML, was created to be robust and easy to parse at the expense of being clean and easy to write. Because of this, a variety of preprocessors have emerged that offer a more terse syntax.

Occasionally, people will avoid preprocessing because it *adds another step*, another layer of abstraction to the end result. However, improvements in code readability and ease of writing far outweigh the inconvenience of this additional step. Furthermore, anything more complex than a static site will require a "build" step anyway, to inject whatever dynamic content the site has.

##How Jade Preprocesses
In the case of Jade, this preprocessing is done by compiling templates into JS and then rendering them to HTML:

![](img/Process.svg)

Because Jade's compiled templates really are just JavaScript functions that output HTML, they can be rendered on both the server and in the browser.

##Comparison with Other Preprocessors
As I mentioned earlier, there are many preprocessors and templating solutions, so it is worth discussing why those may be inadequate.

###HAML
HAML is a very popular, beautiful templating language that was made to replace ERB (Ruby's default templating system) with a more beautiful abstracted markup language. In fact, HAML was one of the major influences on the creation of Jade and can be thanked for many of its features.

However, HAML does have a few problems. It lacks useful features such as mixins and block operations such as extend, and it has a slightly more verbose syntax.

The original implementation of HAML also had the disadvantage of not compiling into JS, so it couldn't be used to write templates that are evaluated on the client side. However, now there are several JS implementations of HAML, the most popular being [haml-js](https://github.com/creationix/haml-js).

###PHP
PHP does not offer any syntactical improvements and must be rendered server side, so it may not be the first thing that comes to mind when discussing these types of languages. However, it is currently the most popular HTML preprocessor - sadly, it is also the worst.

It can hardly be considered a templating language because it has overgrown the scope of a typical templating language and has gained the features of a complete object-oriented programming language. This is a major issue because it encourages the mixing of business logic with templating logic. Combining this with PHP's already awful design, it makes for some pretty horrific code.

###Jinja2
Jinja2 is a templating language for Python. Like PHP, it doesn't have any syntactical improvements and must be rendered server side. Unlike PHP, it has a sensible language design, block-based operations, and it encourages you to keep most of the logic outside of templates. This makes it a good, general-purpose templating language, but it lacks the HTML-specific syntax optimizations that Jade and HAML have.

###Mustache
Mustache is another JS-based templating language, and like Jade it compiles into JavaScript, meaning it can be rendered client side. However, it too lacks HTML-specific syntactical improvements.

There are many other templating languages, but they all suffer from pretty much the same issues, or they just haven't gained a large enough supporting community to be recognized as a major language yet.

##Installation Instructions
To install the Jade compiler, you first need to have Node.js installed. This is a JavaScript interpreter based on V8, that lets you run JS outside of the browser. The installation instructions are available at http://nodejs.org/. Once you have Node.js installed, you can use npm (Node.js Package Manager) to install Jade from the terminal:

```bash
$ npm install jade -g
```

(the `-g` installs Jade globally - without it you wouldn't be able to use the `jade` command)

##Compiling Jade
Now that you have Jade installed, you can use the `jade` command to compile Jade files. For example, if we put some Jade in a file:

```bash
$ echo "h1 Some Jade" > file.jade
```

Then we can use the `jade` command to render that file.

```bash
$ jade file.jade

  rendered file.html

```

which will create `file.html`:

```bash
$ cat file.html
```
```html
<h1>Some Jade</h1>
```

By default `jade` compiles and renders the file, but if we only want it to compile into JS, we can use the `--client` argument.

```bash
$ jade --client file.jade

  rendered file.js

$ cat file.js
```
```js
function anonymous(locals) {
jade.debug = [{ lineno: 1, filename: "file.jade" }];
try {
var buf = [];
jade.debug.unshift({ lineno: 1, filename: jade.debug[0].filename });
jade.debug.unshift({ lineno: 1, filename: jade.debug[0].filename });
buf.push("<h1>");
jade.debug.unshift({ lineno: undefined, filename: jade.debug[0].filename });
jade.debug.unshift({ lineno: 1, filename: jade.debug[0].filename });
buf.push("Some Jade");
jade.debug.shift();
jade.debug.shift();
buf.push("</h1>");
jade.debug.shift();
jade.debug.shift();;return buf.join("");
} catch (err) {
  jade.rethrow(err, jade.debug[0].filename, jade.debug[0].lineno,"h1 Some Jade\n");
}
```

which results in some very ugly JS, mostly due to the debugging information. We can remove that debugging information with the `--no-debug` argument.

```bash
$ jade --client --no-debug file.jade

  rendered file.js

$ cat file.js
```
```js
function anonymous(locals) {
var buf = [];
buf.push("<h1>Some Jade</h1>");;return buf.join("");
}
```

The JS resulting from that could still be optimized a little bit more (and likely will be in future versions of the compiler) but since it's just machine-generated JS, it's not a huge issue. The important part is that this JS can be executed on the client-side to render templates dynamically. This will be covered more in chapter 4.

##Summary
In this chapter, we learned the idea behind preprocessors, and why Jade is awesome. Also, we learned the process Jade uses to compile templates, and how to install/use Jade.

