### Graphics in Purescript

To draw a clock in the browser, we are going to use `purescript-canvas` library.

The browser uses html element `<canvas>` that allows people to draw things with javascript.

Since purescript compiles down to javascript `<canvas>` element can be used to draw our clock.

<br/>
First let's install it in our project,
open your `terminal` and type


`bower install purescript-canvas --save` [^1]

<br/>
Let's also create an `index.html` file which will contain the canvas element.

```
<html>
<head>
</head>
<body>
  <canvas width="800" height="800" id="myCanvas" />

  <script src="./output/bundle.js"></script>
</body>
</html>
```

This is a simple html file, we added the script tag that is pointed to a `bundle.js` file which is the output of our  purescript code.


[^1]: Here the --save `flag` tells `bower` to add it as a `dependency` to the `bower.json` file.
