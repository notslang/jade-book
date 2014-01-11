#Organizing Jade projects
Now that you know how to use all the things that Jade can do, here's when you should use them.

Jade is pretty flexible when it comes to organizing projects; the language itself doesn't impose much structure on your project. However, there are some conventions you should follow, as they will typically make your code easier to manage. This chapter will cover those conventions and best practices.

##General best practices
Most of the good practices that are used when writing HTML carry over to Jade. Some of these include:

 - using a consistent naming convention for id's, class names, and (in this case) mixin names and variables
 - adding alt-text to images
 - choosing appropriate tags to describe content and page-structure

The list goes on, but these are all things you should already be familiar with. So now we're going to discuss some practices that are more Jade-specific.

###Keeping logic out of templates
When working with a templating language, like Jade, that allows you to use advanced logical operations, separation of concerns (SoC) becomes an important practice. In this context, SoC is the separation of business and presentational logic, allowing each part to be developed and updated independently.

An easy point to draw the border between "business" and "presentation" is where data is passed to the template. Business logic is kept in the main code of your application, and passes the data to be presented (as well-formed JSON Objects) to your template engine. From there, the presentation layer takes the data, and performs whatever logic is needed to make that data into a readable web page.

An additional advantage of this separation is that the JSON data can be passed to a template over stdio (to the server-side Jade compiler), or it can be passed over TCP/IP (to be evaluated client-side). Since the template only formats the given data, it doesn't matter where it is rendered, and can be used on both server and client.

![](img/SoC.svg)

-----------------------

**ProTip**
For documenting the format of the JSON data, try [JSON Schema](http://json-schema.org/). In addition to describing the interface between that your presentation layer uses, it can be used in tests to validate the structure of the JSON that your business layer produces.

-----------------------

###Inlining
When writing HTML, it is commonly advised that you don't use inline styles or scripts because it is harder to maintain. This advice still applies to the way you write your Jade.

For everything but the smallest one-page projects, tests, and mockups; you should separate your styles and scripts into different files. These files may then be compiled separately and linked to your HTML with `style` or `link` tags. Or you could `include` them directly into the Jade. But either way, the important part is that you keep it separated from your markup in your source code.

However, in your compiled HTML you don't need to worry about keeping inlined styles out. The advice about avoiding inline styles applies only to your source code and is purely for making your codebase easier to manage. In fact, according to [Best Practices for Speeding Up Your Web Site](http://developer.yahoo.com/performance/rules.html) it is much better to combine your files to minimize HTTP requests, so inlining *at compile time* is a really good idea.

It's also worth noting that, even though Jade can help you inline scripts and styles during compilation, there are better ways to perform these compile-time optimizations. For example, build-tools like [AssetGraph](https://github.com/One-com/assetgraph) can do all the inlining, minifying, and combining you need, without you needing to put code to do so in your templates.

###Minification
Looking back to chapter 5, you'll remember that we can pass arguments through filters to compilers for things like minifying. This feature is useful for small projects, for which you might not want to setup a full build-tool. Also, minification *does* reduce the size of your assets making it a very easy way to speedup your site. However, your markup shouldn't really concern itself with details like how the site is minified, so filter arguments aren't the best solution for minifying. Just like inlining it is much better to do this with a tool like AssetGraph. That way your markup is free of "build instructions".

###Removing style-induced redundancy
A lot of redundant markup is added just to make styling easier: we have wrappers for every conceivable part of the page, empty divs and spans, and plenty of other forms of useless markup. The best way to deal with this stuff is to improve your CSS so it isn't reliant on wrappers and the like. Failing that, we can still use mixins to take that redundancy out of the main part of our code, and hide it away until we have better CSS to deal with it. For example, rather than using this repetitive navigation bar:

```jade
input#home_nav(type='radio', name='nav', value='home', checked)
label(for='home_nav')
  a(href='#home') home

input#blog_nav(type='radio', name='nav', value='blog')
label(for='blog_nav')
  a(href='#blog') blog

input#portfolio_nav(type='radio', name='nav', value='portfolio')
label(for='portfolio_nav')
  a(href='#portfolio') portfolio

//- ...and so on
```

It can be refactored into a reusable mixin:

```jade
mixin navbar(pages)
  - checked = true
  for page in pages
    input(
      type='radio', name='nav', value=page, id="#{page}_nav", checked=checked)
    label(for="#{page}_nav")
      a(href="##{page}") #{page}
    - checked = false
```

Which you can then call later in your markup:

```jade
+navbar(['home', 'blog', 'portfolio'])
```

###Semantic divisions
Sometimes, even though there is no redundancy present, dividing templates into separated mixins and blocks can be a good idea. Not only does it provide encapsulation (which makes debugging easier), but the division represents a logical separation of the different parts of a page.

A common example of this would be dividing a page between the header, footer, sidebar, and main content. These could be combined into one monolithic file, but putting each in a separate block represents their separation, can make the project easier to navigate, and allows each to be extended individually.

###Server-side vs client-side rendering
Since Jade can be used on both the client-side and server-side, we can choose to do the rendering of the templates off of the server. However, there are costs and benefits associated with each approach, so the decision must be made depending on the project.

####Client-side
Using the "Single Page Application" (SPA) design, we can do everything but the compilation of the basic HTML structure, on the client-side. This allows for a static page which loads content from a dynamic backend, and passes that content to Jade templates compiled for client-side usage. For example, we could have simple webapp that, once loaded, fires off a AJAX request to a server running Wordpress with a simple JSON API, and displays the posts it gets by passing the the JSON to templates.

The benefits of this design is that the page itself is static (and therefore easily cacheable), with the SPA design, navigation is much faster (especially if content is pre-loaded), and significantly less data is transferred because of the terse JSON format that the content is formatted in (rather than it being already wrapped in HTML). Also, we get a very clean separation of content and presentation, by actually forcing content to be moved into a CMS, and out of the codebase. Finally, we avoid the risk of coupling the rendering to tightly with the CMS, by forcing all content to be passed over HTTP in JSON - in fact, they are so separated, they don't even need to be on the same server. 

But, there are some issues too - the reliance on JavaScript for loading content means that users who don't have JS enabled will not be able to load content normally and search engines will not be able to see your content without implementing `_escaped_fragment_` URLs. Thus some fallback is needed, whether it is a full site that is able to function without JS, or just simple HTML snapshots, rendered using a headless browser, it is a source of additional work.

####Server-side
We can, of course, render everything on the server-side, and just send regular HTML to the browser. This is the most backwards compatible, since the site will behave just as any static HTML site would, but we don't get any of the benefits of client-side rendering either.

We could still use some client-side Jade for enhancements, but the idea is the same: the majority gets rendered on the server-side, and full HTML pages need to be sent when the user navigates to a new page.

###Build systems
Although the Jade compiler is fully capable of compiling projects on its own, in practice it is often better to use a build system because they can make interfacing with the compiler easier. In addition, build systems often help automate other tasks like minification, compiling other languages, and even deployment. Some examples of these build systems are roots, grunt, and even GNU's make.

![](img/roots.png) ![](img/grunt.png)

For example, roots can recompile Jade automatically each time you save it and even refresh an in-browser preview of that page. Continuous recompilation helps you notice errors sooner and roots helps you avoid the hassle of manually running a command to recompile.
