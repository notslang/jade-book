#Feeding data into templates
So far, we've largely ignored one of the most important parts of templating languages—the ability to feed data into them. But don't worry, for now we will learn interpolation and passing data to templates!

##Syntax
There are several syntactical elements made specifically for displaying content that is passed to the template. Here we will go over what those are.

##Defining variables
First, we're going to start with learning how to define variables inside a template. This is common practice when you are looking to make redundant aspects of a template easy to change. So, here is the syntax for defining a variable:

```jade
- var some_text = "Hello World";
```

If you've worked with JavaScript, this should look very familiar because it is JS. In fact, any JS can be executed in a template, it just needs a dash and a space in front of it.

##Interpolation everywhere!
Now how about actually putting the data into something? For this, we can use interpolation:

```jade
p Jade says #{some_text}!
```

```html
<p>Jade says Hello World!</p>
```

You just wrap the variable that you want to use in `#{` and `}`, and everything in the curly braces is evaluated as code, rather than text. This can be used pretty much anywhere that text can. But what if we don't just want to insert a variable, but want to do something more?

```jade
p 2 times 3 is #{2 * 3}
```

```html
<p>2 times 3 is 6</p>
```

Yep, interpolation can contain full expressions too - pretty much any code you would want to put directly inline. But wait, there's more!

It works in attributes:

```jade
- base_url = "http://slang.cx"
a(href="#{base_url}/about")
```

```html
<a href="http://slang.cx/about"></a>
```

It works in text blocks:

```jade
- i = ['proident', 'dreamcatcher', 'ennui', 'Tonx']

pre
  | #{i[0]} #{i[1]}
  | #{i[2]} #{i[3]}
```

```html
<pre>proident dreamcatcher
ennui Tonx</pre>
```

It even works in tag names:

```jade
- mytag = "section"
#{mytag} Got some content in here
```

```html
<section>Got some content in here</section>
```

------------------------
**ProTip**
Storing your tag names in variables is usually a bad idea because it isn't very natural or expected to be read that way. You may find some use cases, but usually avoid it.

------------------------

##Using variables without interpolation
Writing out the full interpolation syntax when you don't need to actually put a variable inside of a body of text (and have all the text for that attribute or tag directly in the variable) can be a bit annoying. So naturally, Jade gives us a shorter way. Take the following code, for example:

```jade
- i = {"type": "text", "name": "Bob"}
input(type="#{i.type}", value="#{i.name}")
```

```html
<input type="text" value="Bob">
```

This can be rewritten as:

```jade
- i = {"type": "text", "name": "Bob"}
input(type=i.type, value=i.name)
```

```html
<input type="text" value="Bob">
```

Or, consider this example:

```jade
- content = "Richardson leggings Cosby sweater, pariatur locavore Pinterest Schlitz"
p #{content}
```

```html
<p>Richardson leggings Cosby sweater, pariatur locavore Pinterest Schlitz</p>
```

This can be rewritten as this:

```jade
- content = "Richardson leggings Cosby sweater, pariatur locavore Pinterest Schlitz"
p= content
```

```html
<p>Richardson leggings Cosby sweater, pariatur locavore Pinterest Schlitz</p>
```

In each of these instances, we just use an `=` (equal to sign) to indicate that the attribute or block should be the full contents of the variable.

##Escaping
By default, Jade encodes HTML characters for security, so:

```jade
- html_content = "Hello <em>World</em>"
p= html_content
```

```html
<p>Hello &lt;em&gt;World&lt;/em&gt;</p>
```

And, of course:

```jade
- html_content = "Hello <em>World</em>"
p #{html_content}
```

```html
<p>Hello &lt;em&gt;World&lt;/em&gt;</p>
```

This is great for preventing XSS (cross-site scripting) attacks, and even just displaying innocent code examples without needing to encode them yourself. However, it will mess up content that is supposed to be HTML, like the text provided by most content management systems. So, we need a way of telling Jade when it shouldn't escape our text.

```jade
- html_content = "Hello <em>World</em>"
p!= html_content
```

```html
<p>Hello <em>World</em></p>
```

And:

```jade
- html_content = "Hello <em>World</em>"
p !{html_content}
```

```html
<p>Hello <em>World</em></p>
```

All that's needed to change is `=` to `!=` and `#{}` to `!{}`. But let the exclamation point serve as a reminder: letting the content go through the template without escaping can compromise the security of your entire site if that content comes from an untrusted/insecure source.

##Sending the variables to the compiler
Defining all your variables inside your templates would be pretty limiting, so there are a few ways in which we can send data from external sources to templates.

###Compiler arg
The easiest way to send data to the Jade compiler is by just providing it in a JSON object that gets passed to the compiler as an argument.

1. In `file.jade`, enter the following:

    ```jade
    p= my_content
    ```

2. Run the following command in the terminal:
    ```bash
    jade file.jade --obj '{"my_content":"this text is coming through the terminal"}'
    ```

3. And we can see the result in `file.html`:

    ```html
    <p>this text is coming through the terminal</p>
    ```

###Programmatically
Sending variables to Jade programmatically is a bit harder, but offers more flexibility, such as being able to perform rendering within the browser. So, using the same initial file (`file.jade`):


1. Run the following command in the terminal:

    ```bash
    jade file.jade --no-debug --client
    ```

2. And we can see the result in `file.js`:

    ```js
    function anonymous(locals) {
    var buf = [];
    var locals_ = (locals || {}),my_content = locals_.my_content;
    buf.push(
    "<p>" +
    (jade.escape(null == (jade.interp = my_content) ? "" : jade.interp)) +
    "</p>"
    );
    return buf.join("");
    }
    ```

To render this function into HTML, we need to include a set of client-side Jade utilities that are used for escaping and other basic function calls, as well as the code that was outputted into `file.js`. The client-side utilities are avaliable in [runtime.js](https://github.com/visionmedia/jade/raw/master/runtime.js) from the main Jade repo. After all of this is included, you can call the function we created (named `anonymous`) like this:

```js
anonymous(
  {'my_content':'this text is coming through a function call'}
)
```

The preceding code returns the following string of HTML:

```html
<p>this text is coming through a function call</p>
```

------------------------
**Note**
This will error out if the client-side Jade utilities are not included.

------------------------

##Summary
In this chapter, we moved on from the basics of writing Jade, to feeding data into templates. After all, we could hardly call Jade a templating language if we couldn't put anything in our templates. So, we covered both the syntax used to output variables (such as interpolation), and how to actually send the data to the renderer.
