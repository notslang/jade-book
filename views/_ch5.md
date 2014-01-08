#Filters
Much like how Jade is better than writing HTML, there are preprocessed languages for writing other languages. These include languages that compile into CSS, JS, and even specialized subsets of HTML for basic formatting. This book will not attempt to teach any of these to you because, honestly there are already great resources out there for learning all of them. However, it is worth mentioning these languages because Jade has a feature called "filters" that allows you to use several of them right inside your templates.

##The Full List
Thanks to a library called "transformers" by Forbes Lindesay, Jade supports a huge number of these preprocessed languages. You need to install the individual language compilers for most of the transformers, but they're usually pretty easy to install since they're almost all contained in npm modules like the Jade compiler that you installed in chapter 1.

###Template Engines
It is a little strange to use another template engine inside of Jade code, but it is nevertheless allowed because the underlying transformers library supports it.

 - atpl - Compatible with twig templates
 - coffeecup - pure coffee-script templates (fork of coffeekup)
 - dot - focused on speed
 - dust - asyncronous templates
 - eco - Embedded CoffeeScript templates
 - ect - Embedded CoffeeScript templates
 - ejs - Embedded JavaScript templates
 - haml - dry indented markup
 - haml-coffee - haml with embedded CoffeeScript
 - handlebars - extension of mustache templates
 - hogan - Mustache templates
 - jade - robust, elegant, feature rich template engine
 - jazz
 - jqtpl - extensible logic-less templates
 - JUST - EJS style template with some special syntax for layouts/partials etc.
 - liquor - extended EJS with significant white space
 - mustache - logic less templates
 - QEJS - Promises + EJS for async templating
 - swig - Django-like templating engine
 - templayed - Mustache focused on performance
 - toffee - templating language based on coffeescript
 - underscore
 - walrus - A bolder kind of mustache
 - whiskers - logic-less focused on readability

###Stylesheet Languages
These languages can be extremely useful for writing and generating CSS, but normally are difficult to compile when they are inside of other files. Jade solves this problem by allowing you to use them through filters.

 - less - LESS extends CSS with dynamic behavior such as variables, mixins, operations and functions.
 - stylus - revolutionary CSS generator making braces optional
 - sass - Sassy CSS

###Minifiers
The minifiers are not incredibly useful, but like the template engines, they are supported by the underlying library and are therefore worth mentioning.

 - uglify-js - No need to install anything, just minifies/beautifies JavaScript
 - uglify-css - No need to install anything, just minifies/beautifies CSS
 - ugilify-json - No need to install anything, just minifies/beautifies JSON

###Other
This section includes
 - cdata - No need to install anything, just wraps input as `<![CDATA[${INPUT_STRING]]>` with the standard escape for `]]> (]]]]><![CDATA[>)`.
 - cdata-js - same as cdata, but with surrounding comments suitable for inclusion into a HTML/JavaScript `<script>` block: `//<![CDATA[\n${INPUT_STRING\n//]]>`.
 - cdata-css - same as cdata, but with surrounding comments suitable for inclusion into a HTML/CSS `<style>` block: `/*<![CDATA[*/\n${INPUT_STRING\n/*]]>*/`.
 - verbatim - No need to install anything, acts as a verbatim passthrough `${INPUT_STRING}`
 - coffee-script - A little langauge that compiles into JavaScript
 - cson - coffee-script based JSON format
 - markdown - You can use marked, supermarked, markdown-js or markdown
 - component-js - npm install component-builder options: {development: false}
 - component-css - npm install component-builder options: {development: false}
 - html2jade - npm install html2jade - Converts HTML back into Jade

##Examples
Because of the vast number of languages that can be used in filters, I'm not going to give examples for all of them (that would get really redundant). But here are the most popular ones:

###Markdown

```jade
:markdown
  Markdown is **much** easier to write than that _ugly_ [HTML](http://www.w3.org/html/‎).
```

```html
<p>Markdown is <strong>much</strong> easier to write than that <em>ugly</em> <a href="http://www.w3.org/html/‎">HTML</a>.</p>
```

###CoffeeScript

```jade
:coffeescript
  square = (x) -> x * x
  cube = (x) ->
    square(x) * x
```

```html
<script type="text/javascript">
  (function() {
    var cube, square;

    square = function(x) {
      return x * x;
    };

    cube = function(x) {
      return square(x) * x;
    };
  }).call(this);
</script>
```

###Stylus

```jade
:stylus
  border-radius()
    -webkit-border-radius arguments
    -moz-border-radius arguments
    border-radius arguments
    
  body
    font 12px Helvetica, Arial, sans-serif
    
  a
    color purple

    .button
      border-radius 5px
```

```html
<style type="text/css">
  body {
    font: 12px Helvetica, Arial, sans-serif;
  }

  a {
    color: #800080;
  }

  a .button {
    -webkit-border-radius: 5px;
    -moz-border-radius: 5px;
    border-radius: 5px;
  }
</style>
```

##Passing Arguments
Since compilers often take options, Jade has a syntax for passing options to filters. The syntax is the same as specifying attributes for tags, except 

For example, if we pass the `minify` option to the Stylus filter, then the output is minified, rather than pretty-printed as it was in the example above:

```jade
:stylus(minify=true)
  p
    color red
  b
    font-weight bold
    color blue
```

```html
<style type="text/css">p{color:#f00}b{font-weight:bold;color:#00f}</style>
```

For a full list of arguements that can be passed, see the [transformers repository](https://github.com/ForbesLindesay/transformers).

##Summary
In this chapter, we covered our first "feature" of the Jade language: filters, a way to automatically compile other preprocessed languages (like Stylus, Markdown, or CoffeeScript) directly in templates.

