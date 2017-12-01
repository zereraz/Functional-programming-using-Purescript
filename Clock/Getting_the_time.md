### Getting the time

We have our clock outer circle and our hours written. Now before we draw the hands of our clock to show the time, let's get the time first!
So we will get the time from Javascript itself. Purescript has good foreign function interface, what that means is that you can easily interace with existing Javascript code.

Make a file `src/Main.js`, we named it `Main.js` as the name should match it's Purescript counter part where we will be using it.

```
exports.getNativeTime = function() {
    var now = new Date();
    return {h:now.getHours(), m: now.getMinutes(), s: now.getSeconds()};
}
```

`exports.myFunctionName` is how we expose Javascript functions and allow them to be imported in Purescript.
`var now = new Date()` creates a new Date object and stores it in the variable now.
`return value` returns the value when this function is called.
`{h:now.getHours(), m: now.getMinutes(), s: now.getSeconds()};` this is a Javascript object, very similar to Purescript record.

So we plan to import this function and get the record out of it containing our `h m s` which is hours, minutes and seconds respectively.

*Note:*
```
return in Javascript is very different from return in Purescript
```

```
type CurrentTime = {h :: Int, m :: Int, s :: Int}
foreign import getNativeTime :: forall eff. Eff eff CurrentTime
```
We first created a type alias called `CurrentTime` which is matching the format of our Javascript object that we are returning.
It is just a way of telling the compiler that where ever you see `CurrentTime` it means `{h :: Int, m :: Int, s :: Int}`

With `foreign import getNativeTime` we import our Javascript function.
`::` means we are going to write the type of our imported function after it.

Since we are going outside of Purescript and calling Date api's of Javascript there could be side-effects, so we create a type variable `eff` which is a variable we can use when telling the compiler the type of something, which represents the side-effects that we will do with this function.

`Eff` is the structure around our value `CurrentTime` that we want. This is to keep the side-effect code under control and easy to spot. Here `forall eff. Eff eff CurrentTime` is the type of our function which means it is giving information about the values of the function. `Eff` takes 2 things, the effects and the value stored in it, here `Eff eff CurrentTime` means the side-effects can be any `eff` and the value stored in `Eff` is of type `CurrentTime`.

*Note:*
```
Here `eff` is not giving any benefit as of now but later we will see that we can specify which side-effects are happening where.
```

Whenever in do block we see `myVal <- myFunction arg` the return type of `myFunction` was Eff so with `<-` we get the value out of it.
`time <- getNativeTime` get's us our record CurrentTime.
