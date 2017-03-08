# Third Party JavaScript Libraries
## Externs
## JavaScript code not touched by Google Closure Compiler

!SLIDE

Our Third Party JavaScript library `greeting.js`

    @@@javascript
    var foo = {};
    foo.bar = function(greeting) {
      return greeting + " friend";
    }
!SLIDE

Calling `greeting.js` from ClojureScript

    @@@clojure
    (.bar js/foo "hello")

!SLIDE

Everything works fine for development, testing, QA, and production builds

!SLIDE

Production application

    @@@javascript
    foo.w("hello");
    "foo.w is not a function"

Why did this only happen?

!SLIDE

Optimizations `:advanced`

`foo`.`bar`(`"hello"`)

Google Closure Compiler

`foo`.`w`(`"hello"`)

For advanced compilation Google Closure Compiler is very aggressive renaming to minimize code size


!SLIDE

To correct this Google Closure Compiler needs to know what names it's not allowed to mangle

This happens via an "externs" (external) file

Our Externs File

    @@@javascript
    var foo = {};
    foo.bar = function() {};

!SLIDE

Optimizations `:advanced`

`foo`.`bar`(`"hello"`)

Google Closure Compiler - `foo`.`bar`

`foo`.`bar`(`"hello"`)

!SLIDE

# So all you need to do is go through all your JavaScript libraries and write down all the names they export
# :(

!SLIDE

Worst Case
Fully manual

JS Library Most Common case
Can't Use

!SLIDE

Better Case
Uncanny Valley
Have to manually wireup externs file

JS Library    Intersection      Externs
Can't Use     Happiness         Lots of painful debugging

!SLIDE

Best Case
Still have uncanny valley

JS Library    Intersection      Externs                    ClojureScript Wrapper
Can't Use     Happiness         Lots of painful debugging  CLJSJS

!SLIDE

Notice this only works for one version of the library.

`1.2.0` -> `1.2.4` -> `1.3.0`

New version means redoing everything again.

!SLIDE

What do we do when there is no externs available or the default externs don't cover the library?

Two ways to escape the pit of despair

- Externs inference
- `cljs-oops` library

!SLIDE

# Externs Inference
## ClojureScript can generate externs for us as of `1.9.546`

!SLIDE

## `:infer-externs true`
Turns on Externs Inference and writes an externs file `inferred_externs.js`
## `(set! *warn-on-infer* true)`
Turns on inference warnings

!SLIDE

Three types of inference warnings

- Use of an unknown JavaScript Object
    - "Cannot infer target type for ..."
- Use of a generic JavaScript Object
    - "Adding extern to Object for ..."
- Calling an Unknown method or property
    - "Cannot resolve property ..."

!SLIDE

    @@@clojure
    (defn cloudy [outside]
      (.getClouds outside))

"Cannot infer target type for ..."

ClojureScript doesn't know what type `outside` is, we need to tell ClojureScript that outside is a `Weather` object

!SLIDE

`? outside type` -> `outside`

`Weather` -> `outside`

!SLIDE

Type Hint `outside` with `js/Weather`

    @@@clojure
    (defn cloudy [^js/Weather outside]
      (.getClouds outside))

!SLIDE

    @@@clojure
    (defn cloudy [^js/Weather outside]
      (let [clouds (.getClouds outside)]
        (.getType clouds)))

"Adding extern to Object for ..."

ClojureScript assumes that the result of `(.getClouds outside)` is a generic JavaScript object.
ClojureScript is warning that it's writing `Object.getType;` into `inferred_externs.js`

!SLIDE

`outside.getClouds()` -> `Object`

`outside.getClouds()` -> `Clouds`

!SLIDE

Add return type to `getClouds` and add `Clouds` externs to `inferred_externs.js`

    @@@javascript
    /*
     *  @return {Clouds}
     */
    Weather.prototype.getClouds;

    var Clouds;
    Clouds.prototype.getType = function() {};

!SLIDE

    @@@clojure
    (defn cloudy [^js/Weather outside]
      (let [clouds (.getClouds outside)]
        (.frog clouds)))

"Cannot resolve property ..."

ClojureScript doesn't know the method / property you're trying to use. The `frog` method is not in the externs file

!SLIDE

Change ` (.frog clouds)` to `(.getType clouds)`

Add `frog` to `inferred_externs.js`

    @@@javascript

    Clouds.prototype.frog = function() {};

!SLIDE

# Sidestep externs entirely with `cljs-oops` library

!SLIDE

Idea is to use `aget` or `goog.object/get` instead of calling methods / properties directly

    @@@clojure
    (defn cloudy [outside]
      (let [clouds (.call (aget outside "getClouds") outside)]
        (.call (aget clouds "getType") clouds)))

&nbsp;

    @@@javascript
    outside.getClouds();
    outside["getClouds"].call(outside); // No optimization
    a.getClouds.call(a); // Simple and Advanced

&nbsp;

    @@@javascript
    clouds.getType();
    clouds["getType"].call(); // No optimization
    b.getType.call(b); // Simple and Advanced

!SLIDE

Using `aget` / `goog.object/get` is a very manual and error prone

`cljs-oops`

- automates the process with macros
- has extensive validation during development
- emits optimized code during advanced compilation

!SLIDE

`cljs-oops` covers `get`, `set`, `call` and `apply`

    @@@clojure
    (ocall foo ["bar" "baz"] 1 2 3)
    (.call (aget foo "bar" "baz") foo 1 2 3)

    (oapply foo "bar" "bar" [1 2 3])
    (.apply (aget foo "bar" "baz") foo [1 2 3])

    (oget home "floor" "living-room")
    (aget home "floor" "living-room")

    (oset! home  "floor" "living-room" "300 sqft")
    (aset home "floor" "living-room" "300 sqft")

!SLIDE

Not just useful for external JavaScript incredibly useful for native JavaScript objects built dynamically

Additional functionality for JavaScript objects
- `?` soft operation that stops when key does not exist

        @@@clojure
        (oget #js {:house {:bedroom {:color "red"}}} "house" "?livingroom" "color")
        ;; => nil
- `!` punch operator, if the key does not exist create it

        @@@clojure
        (oset! #js {:house {:bedroom {:color "red"}}} "house" "!livingroom" "!color" "green")
        ;; => #js {:house {:bedroom {:color "red"}} {:livingroom {:color "green"}}}

!SLIDE

Check CLJSJS first then see if externs have already been created
Use externs inference or `cljs-oops` when there are no externs or the default externs don't cover what you need
