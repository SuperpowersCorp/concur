# Concur

A client side web ui framework for Haskell that is not FRP (think Reflex), or Elm, or React.

## The UI problem

There are lots of options for building web UIs. I have built large UIs with Plain Javascript, Angular, React, Elm, and Reflex, among others. However something seems to be missing from all of them.

1. JS based UI libs tend to be very unstructured and require a lot of effort from the programmer to keep things maintainable. React is the only exception I've seen, however it too comes with its own complications due to the fact that it is written in JS.
2. Elm has a lot of things going for it, but ultimately, "The Elm Architecture" feels very constrained. There are many well known abstractions for structuring code that we would like to use, but can't. For example, we can't use monads to build UIs incrementally. There are no monad transformers, so no using StateT to manage state. Even the composability of multiple widgets suffers because of how rigidly we must adhere to the elm architecture and must loop everything throgh a state modification and a render function. This codebase started out as an attempt to keep Elm's advantages, while opening up new avenues to structuring code.
3. Reflex/FRP has brilliant ideas at its core but ultimately suffers from annoying flaws due to its flexibility and complexity. FRP is NOT the easiest model to think and reason about. I've also found it difficult to refactor. For example, if you have to extract a bunch of events from deep within the DOM (though that may just be my bad coding practices). There are unsolved problems (such as handling global popups) that usually necessitate peppering random IO effects throughout the codebase, which can cause race conditions. In my opinion, Reflex is currently the best solution for UIs, in that it gives you the full power of Haskell, and is quite fun to use, but it can be improved upon.

## The solution
This library was originally conceived as a way to improve Elm by adding -
1. Easy widget composition. If you have a `counter` widget, then adding two of them in the same page should be something as simple as `counter <|> counter`
2. Remove the focus on State modification. Make rendering a `Monad` so that it's possible to use `StateT` transformer to get easy state modification back.
3. Use *inversion of control* to make everything seem synchronous and simplify code. Waiting for a click event should be as simple as `click >>= doSomething` with no callbacks in sight.
4. Using inversion of control made effects more expressive, while still not as unstructured as Reflex. An effect in Concur is simply `IO a`. If you want to display a widget every 10 seconds, it's as simple as `forever $ liftIO (threadDelay 10*1000000) >> doSomething`. If you want to do the same thing every 10 seconds *and* on every click, it's as simple as `forever $ (getClick <|> liftIO (threadDelay 10*1000000) >>= doSomething`.
5. Eliminating the difference between state modification and view rendering. So a new widget can be simply - `div (class_ "hello") [text "Here are some widgets", counter, someOtherWidget]`. This wires up both the view rendering and the action handling for all nested widgets, without having to manually manage each separately.

## Examples

NOTE: You can see the examples in action at - https://ajnsit.github.io/concur/

1. [Click Counting Example](examples/ClickCounter.hs) - Count total number of clicks, with a button that also increments the click count by 10.
2. [TodoMVC Example](examples/Todos.hs) - The canonical TodoMVC example, with views modeled after the one in Elm. It works just like the Elm example but does not have CSS.
