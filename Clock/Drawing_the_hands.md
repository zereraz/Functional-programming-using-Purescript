### Drawing the hands

Now time to draw the hands of our clock. Just like how we created the drawOuterCircle function before. First let's create a global record called `clockCenter` so that all our functions can access the center of our clock

```
clockCenter = {x: 150.0, y: 150.0}
```

Now we can get to the `drawHand` function

```
drawHand ctx x y = do
  _ <- beginPath ctx
  _ <- moveTo ctx clockCenter.x clockCenter.y
  _ <- lineTo ctx x y
  _ <- closePath ctx
  stroke ctx
```
Pretty similar to what we have done so far.
`moveTo` tells  canvas to go to that x y location, `lineTo` draws the line from where we were(`moveTo`) to where we specify.

Now that we can draw a hand, let's write code for drawing it based on the current time.

```
drawHands time ctx = do
  let secondsAngleGap = (2.0 * pi) / 60.0
      minutesAngleGap = (2.0 * pi) / 60.0
      hoursAngleGap   = (2.0 * pi) / 12.0
      secondOffset    = 0.75
      minuteOffset    = 0.6
      hourOffset      = 0.45
      convertedHour   = toNumber $ mod time.h 12
      m               = toNumber time.m
      s               = toNumber time.s
      sx              = clockCenter.x + (clockArc.r * secondOffset) * cos (s * secondsAngleGap - (pi / 2.0))
      sy              = clockCenter.y + (clockArc.r * secondOffset) * sin (s * secondsAngleGap - (pi / 2.0))
      mx              = clockCenter.x + (clockArc.r * minuteOffset) * cos (m * minutesAngleGap - (pi / 2.0))
      my              = clockCenter.y + (clockArc.r * minuteOffset) * sin (m * minutesAngleGap - (pi / 2.0))
      hx              = clockCenter.x + (clockArc.r * hourOffset) * cos (convertedHour * hoursAngleGap - (pi / 2.0))
      hy              = clockCenter.y + (clockArc.r * hourOffset) * sin (convertedHour * hoursAngleGap - (pi / 2.0))
  _ <- drawHand ctx sx sy
  _ <- drawHand ctx mx my
  drawHand ctx hx hy
```

`drawHands` take `CurrentTime`, canvas context and draw's hour, minute and second hand for our clock.
`secondsAngleGap` `minutesAngleGap` and `hoursAngleGap`is the per second minute and hour angle respectively. So hour hands would rotate that amount of angle when any of their value changes. `secondOffset` `minuteOffset` and `hourOffset` represent their length with respect to radius, 0.75 means 75% of the radius.

`convertedHour` is to convert our 24 hour value from Javascript to work well with 12 hour clock. `m` and `s` are turned to `Number` type so that compiler has no issue with using them with other `Number`'s' in calculations.

`mx` `my` are the x y coordinates for the minute hand, similarly for `sx` `sy` `hx` `hy`, calcuations follow the same `x = radius * cos(theta)` and `y = radius * sin(theta)`
Here we are subtracting `pi/2.0` in the angle so that we start from the `12`(`-90 degree`) hour point and not `3` hour point (`0 degree`)

Then we just call our drawHand function with the appropriate x y values.
