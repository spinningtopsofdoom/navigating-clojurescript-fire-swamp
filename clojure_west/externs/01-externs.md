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

Optimizations `:none`

`foo`.`bar`(`"hello"`)

Google Closure Compiler

`foo`.`bar`(`"hello"`)

!SLIDE

Optimizations `:whitespace`

`foo`.`bar`(`"hello"`)

Google Closure Compiler

`foo`.`bar`(`"hello"`)

!SLIDE

Optimizations `:simple`

`foo`.`bar`(`"hello"`)

Google Closure Compiler

`foo`.`bar`(`"hello"`)

!SLIDE

Optimizations `:advanced`

`foo`.`bar`(`"hello"`)

Google Closure Compiler

`foo`.`w`(`"hello"`)

!SLIDE

What happened? In `:advanced` optimizations Google Closure Compiler can rename any names and doesn't know about `bar` method on `foo`
Solution: tell Google Closure Compiler what names it's not allowed to rename (externs file)

!SLIDE

Our Externs File

    @@@javascript
    foo.bar = function() {};

!SLIDE

Optimizations `:advanced`

`foo`.`bar`(`"hello"`)

Google Closure Compiler - `foo`.`bar`

`foo`.`bar`(`"hello"`)

!SLIDE

# Problem solved right?
## Manual Bookkeeping for all Third Party method / functions / variables etc.

!SLIDE

Worst Case
Fully manual

JS Library
Can't Use

!SLIDE

Better Casw
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

3 different sets of maintainers synchronizing across different versions

!SLIDE

So when there is no externs available or the default externs don't cover the library?
Are we caught in the pit of despair?

Two solutions
- Externs inference
- `cljs-oops` library

!SLIDE

# Externs Inference
## ClojureScript can generate externs for us. Started at version `1.9.546`
## Turn on with compiler option `:infer-externs true`

!SLIDE

Example

    @@@clojure
    (defn cloudy [outside]
      (let [clouds (.getClouds outside)]
        (.getType clouds)))

!SLIDE

Turn on Warnings `(set! *warn-on-infer* true)`

`? outside type` -> `outside` -> `outside.getClouds()`
`? cloud type` -> `clouds` -> `clouds.getType()`

    @@@clojure
    (defn cloudy [outside]
      (let [clouds (.getClouds outside)]
        (.getType clouds)))

Gives warning "Cannot infer target type for ..." when ClojureScript does not know the type of JavsScript object with method (TODO work on this)

!SLIDE

`js/Weather` -> `outside` -> `outside.getClouds()`

    @@@clojure
    (defn cloudy [^js/Weather outside]
      (let [clouds (.getClouds outside)]
        (.getType clouds)))

New warning "Adding extern to Object for ..." since the return type for `getClouds` is unknown

`js/Weather` -> `outside` -> `outside.getClouds()` -> `? type`

!SLIDE

Add return type for `getClouds`

`js/Weather` -> `outside` -> `outside.getClouds()` -> `js/Clouds`

    @@@javascript
    /*
     *  @return {Clouds}
     */
    Weather.prototype.getClouds;

    var Clouds;
    Clouds.prototype.getHype;

!SLIDE

New warning "Cannot resolve property ..." when JavaScript type does not have method / property from externs

correct externs definition

    @@@javascript
    Clouds.prototype.getHype;

    @@@javascript
    Clouds.prototype.getType;

!SLIDE

TODO Just a CFP for now https://github.com/clojure/clojurescript/wiki/Singleton-Pattern-Externs-Inference-Support

For some libraries (e.g. `jQuery` or `D3`) all operations happen on a single interface

So every operation returns the same type meaning we can prevent all return inference ("Adding extern to Object for ...")

`weather` -> `js/Waether for all methods`

!SLIDE

# Sidestep externs entirely with `cljs-oops` library

!SLIDE

Idea is to use `aget` or `goog.object/get` instead of calling methods / properties directly

    @@@clojure
    (defn cloudy [outside]
      (let [clouds (.call (aget outside "getClouds") outside)]
        (.call (aget clouds "getType") clouds)))

    @@@javascript
    outside.getClouds();
    outside["getClouds"].call(outside); // No optimization
    a.getClouds.call(a); // Simple and Advanced

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
