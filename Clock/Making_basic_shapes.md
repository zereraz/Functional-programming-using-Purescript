### Drawing basic shapes

First let's import the `purescript-canvas` library in our `Main` module

`import Graphics.Canvas`

<br/>
Every program starts with the `main` function that is found in the `Main` module.

So now let's write the `main` function

```
main = do
  maybeCanvasElem <- getCanvasElementById "myCanvas"
```

<br/>
We start the main function with the `do` block, which help's us separate the side effects and pure code.
There is more to do with the `do` block but we will learn the details of that later.

**Note:**
```Explain the <- arrow in do block with examples ```

<br/>
So first we need to get our canvas element on which we would draw our clock,
`getCanvasElementById` is a function in `Graphcs.Canvas` module that takes the `id` of the canvas element as the argument and returns this canvas element.

**Note:** 
```Here the id argument is an html attribute for the canvas```

<br/>
But this canvas element may or may not exist in our html file, so there are chances for the value to be `null`, purescript represents
such nullable values as `Maybe` so that you handle the case when things are null.

**Note :** ```Maybe helps prevent null pointer bugs that you may have encountered in other programming languages```

<br/>
Now that we have this Maybe Canvas value, for the sake of simplicity let's not handle the null case for now, later we will learn how to do it properly.

```
main = do
  maybeCanvasElem <- getCanvasElementById "myCanvas"
  let canvas = unsafePartial(fromJust maybeCanvasElem)
```

We are calling two functions here, `fromJust` which get's the value out of `Maybe Canvas` to give us `Canvas`. And we call the `unsafePartial` to tell the compiler that we do not care to handle the null scenario.
If we do not call `unsafePartial` it would not compile as the compiler would tell us that we forgot to handle the null case.

Here the `let` keyword allows us to give names to `expressions`, here the output of both the functions is named `canvas`.
This is similar to variables in other languages but since purescript is a purely functional programming language it does not allow you to change any of such stored values.

```
main = do
  maybeCanvasElem <- getCanvasElementById "myCanvas"
  let canvas = unsafePartial(fromJust maybeCanvasElem)
  ctx <- getContext2D canvas
```

`getContext2D` returns the drawing context on the canvas, which allows us to draw on our canvas.

Now we have everything setup, let's draw our first shape in Purescript.

```
main = do
  maybeCanvasElem <- getCanvasElementById "myCanvas"
  let canvas = unsafePartial(fromJust maybeCanvasElem)
  ctx <- getContext2D canvas
  let myCircle = {x:150.0, y:150.0, r:50.0, start:0.0, end: (2.0 * pi)}
  _ <- setStrokeStyle "#000000" ctx
  _ <- arc ctx myCircle
  stroke ctx
```


Here `myCircle` is a `record`, it is a collection of values grouped together with labels.
Here `x y r start end` are labels, which allow us to access the values.

**Eg:**
```myCircle.x gives us the 150.0 value```

Records are used to create collection of values that make sense to be grouped together.

<br/>
**Note:**
```Records correspond to Javascript's objects```

`_ <- functionCall argument`
Here the `_` is a wildcard to indicate that we just want to call the `functionCall` and ignore it's output.

<br/>
We call `setStrokeStyle` function give color to the boundary of our circle

For drawing circle we are using a function called `arc` from `Graphics.Canvas` module which expects a record like `myCircle`.
Here x and y values are the x y coordinates of the center of the arc, r is the radius of the circle, start is the start angle of the arc
and end is the end angle of the arc.

**Note:**
```The angle is given in radians, we want a circle so we go from 0.0 to 2*pi, which is 0 to 360 degrees```

<br/>
Finally we call `stroke` which actually draw's the path that `arc` function defined.

