### Writing the hours

So this is the code we wrote so far

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

If we keep writing all the code in the main function then it would get too big. It would be very hard to know what our program is doing as our code increases.
Here it makes sense to create a new function which when called creates our circle for us.
This increases code re-use as in future if we ever need to draw a circle we can call this.

```
drawOuterCircle ctx = do
  let myCircle = {x:150.0, y:150.0, r:50.0, start:0.0, end: (2.0 * pi)}
  _ <- beginPath ctx
  _ <- arc ctx myCircle
  _ <- setStrokeStyle "#000000" ctx
  _ <- setLineWidth 1.0 ctx
  _ <- closePath ctx
  stroke ctx

main = do
  maybeCanvasElem <- getCanvasElementById "myCanvas"
  let canvas = unsafePartial(fromJust maybeCanvasElem)
  ctx <- getContext2D canvas
  drawOuterCircle ctx
```
When we are drawing various shapes it is necessary we do not mess up drawing shapes that we draw. So it is to call `beginPath` function which tells canvas to start a new path and reset the current path. Now even if we draw something else it would not affect this function as the path has been reset. `closePath` joins the current point to the start of the path. After this we call `stroke` which draw's the path we created.

Now let's start with drawing the hours of the clock.

```
drawHours ctx clockArc = do
  tm <- (measureText ctx "1")
  let hours = map toNumber (-2..9)
      hourAngleGap = (2.0 * pi) / 12.0
      radiusOffset = 0.85
      offset = tm.width
```
`drawHours` takes 2 parameters, the canvas context and our myCircle record that we used to draw the circle of the clock.
`measureText` is a function that takes some text and returns a record which contains the width of the text after it will be drawn. This will help us with aligning our text and prevent it from skewing. `map` is a function that takes another function and a structure like `List`, `Array`, applies this function to each of the element of that structure and returns a new structure with the new computed values. `(..)` is an alias for the `range` function, this just generates an `Array` which is a list of numbers from start value to the end value we provided. So `map toNumber (-2..9)` generates numbers from -2 to 9 which are of type `Int` but we want `Number` so we call toNumber on each of the element of that `Array` using the `map` function.

`hourAngleGap` is the angle gap between each hour, `360/12` in degrees. `radiusOffset` right now helps us with how far will we draw our hours from the center, `0.85` is like `85%` of the radius.

Now let's add the final part to draw the hours. We have to calculate the x and y coordinates of our text and finally draw it.
```
drawHours ctx clockArc = do
  tm <- (measureText ctx "1")
  let hours = map toNumber (-2..9)
      hourAngleGap = (2.0 * pi) / 12.0
      radiusOffset = 0.85
      offset = tm.width
      -- Getting the correct x,y coordinates for where the hour should be written
      xcoords = map (\hour -> clockArc.x - offset + (clockArc.r * radiusOffset) * cos (hour * hourAngleGap)) hours
      ycoords = map (\hour -> clockArc.y + offset + (clockArc.r * radiusOffset) * sin (hour * hourAngleGap)) hours
      zippedCoords = zip xcoords ycoords
      -- Writing the text for those coordinates
  _ <- zipWithA (\(Tuple x y) index -> fillText ctx (show index) x y) zippedCoords (1..12)
  pure ctx
```

`xcoords` and `ycoords` is using the `radius * cos(theta)` and `radius * sin(theta)` respectively for each of the values of the array. `clockArc.x - offset` helps us to push our hours to the left side, otherwise without it our clock hours look skewed. Here the `map` function takes an anonymous function where we do our calculations.
Now that we have 2 different Array's of xcoordinates and ycoordinates, it would be nice to combine them into one single structure, `(x,y)`. A `Tuple` allows us to do that. `zip` is a function that takes 2 `Array`'s and for each `Array` get's one value out, turns them into a `Tuple` and keeps doing that till we reach the end of either of the `Array`'s. 
Now we have a zipped Array, and we want to perform a function on each element of this array we could use `map` but we want to know the index of each element so that we know what to draw for each particular x y coordinate, `zipWith` is a function that helps us with this. `zipWith` takes a function and 2 `Array`'s and just like `zip` it calls our function with element from each array as it's argument. But unlike the previous `map` functions we wrote which were pure functions i.e without any side-effects, we want to perform fillText function which does perform a side-effect(drawing on the screen). To achieve this we use a variant of `zipWith` called `zipWithA`.

`(Tuple x y)` is pattern matching inside the anonymous function, which allow's us to access our x y values that were stored as a `Tuple`.


