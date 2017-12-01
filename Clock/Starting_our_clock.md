### Starting our clock

By now we should have a clock with the current time visually. Each time you refresh your browser you will see the second hand changing as each time we refresh our code get's the latest time and draw's the clock based on that time.

Let's first construct a function that is responsible for connecting all the aspects of the clock i.e it will first draw the circle, then draw the hours then draw the hands. This will make it easy for us to refresh our clock as we will keep calling this function to get a new clock each time.

```
drawClock ctx = do
  _ <- drawOuterCircle ctx
  currentTime <- getNativeTime
  _ <- drawHours ctx
  drawHands currentTime ctx
```

This function we can call from our main function,

 ```
main = do
  maybeCanvasElem <- getCanvasElementById "canvas"
  let canvas = unsafePartial $ fromJust maybeCanvasElem
  ctx <- getContext2D canvas
  _ <- drawClock ctx
 ```

Now let's add the final piece and get the clock to refresh itself on it's own.

```
main = do
  maybeCanvasElem <- getCanvasElementById "canvas"
  let canvas = unsafePartial $ fromJust maybeCanvasElem
  ctx <- getContext2D canvas
  setInterval 1000 do
    _ <- clearRect ctx {x: 0.0, y: 0.0, w: 800.0, h: 800.0}
    _ <- drawClock ctx
    pure unit
```

First we added a function called `setInterval` which takes time in milliseconds and then run's the `do` block we give it. We put 1000 to make it call our `do` block every 1 second.

In our `index.html` file that we created at the start we gave the `<canvas />` element width and height as 800 pixels.
`<canvas width="800" height="800" id="myCanvas" />`

Now if we want to refresh our clock we can erase the whole of canvas and then draw our clock each time.
This is what `clearRect` does, clearRect takes canvas context and then it takes a record with x coordinate, y coordinate from where it should start the rectange and w h are width and height. So this rectangle erases every pixel that comes inside it, including our clock.

After we clear the whole canvas, we call our drawClock function and that is it.
Now at the end we wrote called a function `pure` with our canvas context as the argument. This is necessary because at the end of a do block the last expression whatever it's value is, it is returned from that whole block. This do block is inside a setTimeout, if we didn't write `pure ctx` then the last expression was of drawClock which returns a canvas context as the return value. But that is not correct as setTimeout has already defined what it expects, it expects value of type Unit wrapped inside our Eff structure. `unit` is a function that returns us value of type Unit and to wrap it in our Eff structure we use a function called `pure` that automatically wraps things in do block's context which in this case was Eff.

Now if you run the code and refresh the browser we get our working clock.
