# Externs
## Third Party JavaScript Libraries
## Not Handled by Google Closure Compiler

!SLIDE

## JavaScript Library

    @@@javascript
    var foo = {};
    foo.bar = function(greeting) {
      return greeting + " friend";
    }

## Calling library in ClojureScript

    @@@clojure
    (.bar js/foo "hello")
!SLIDE

## Everything works fine in

- Development
- Testing
- QA
- Production Builds

!SLIDE

## Error in production application

![foo.w is not a function](../../images/foo_not_a_function.png)


## Complied JavaScript

    @@@javascript
    foo.w("hello");

What happened to `foo.bar`?

!SLIDE

## Google Closure Compiler Renaming

Optimizations `:advanced`

`foo`.`bar`(`"hello"`)

Google Closure Compiler

`foo`.`w`(`"hello"`)

!SLIDE

# Inform Google Closure about external names
## An "externs" file

    @@@javascript
    var foo = {};
    foo.bar = function(greeting) {};

!SLIDE

Optimizations `:advanced`

`foo`.`bar`(`"hello"`)

Google Closure Compiler - `foo`.`bar`

`foo`.`bar`(`"hello"`)

!SLIDE

# So all we need to do is go through all your JavaScript libraries and write down all the names they export

!SLIDE

![JavaScript Library with no Externs](../../images/extern_js_library.svg)

!SLIDE

![JavaScript Library with Externs](../../images/extern_javascript_externs.svg)

!SLIDE

![ClojureScript Wrapper for JavaScript Library with Externs](../../images/extern_clojurescript_library.svg)

!SLIDE

![Multiple JavaScript versions](../../images/extern_library_versions.svg)

!SLIDE

# Caught in the Pit of Despair
## Only most popular libraries are readily usable

!SLIDE

# We can escape the Pit Of Despair

- ClojureScript Externs inference
- `cljs-oops` library
!SLIDE

# Externs Inference
## ClojureScript can generate externs for us as of `1.9.456`

!SLIDE

## Compiler Option
## `:infer-externs true`
Turns on Externs Inference and writes an externs file `inferred_externs.js`
## Infer Waring Flag
## `(set! *warn-on-infer* true)`
Turns on inference warnings

!SLIDE

# Three types of inference warnings

- Use of an unknown JavaScript type
- Using Base JavaScript Object
- Calling an Unknown method or property  on a known extern

!SLIDE

    @@@clojure
    (defn cloudy [outside]
      (.getClouds outside))

"Cannot infer target type for ..."

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

`outside.getClouds()` -> `Object`

`outside.getClouds()` -> `Clouds`

!SLIDE

Wrap `getClouds` function in ClojureScript

    @@@clojure
    (defn ^js/Clouds get-clouds [^js/Weather outside]
      (.getClouds outside))

    (defn cloudy [outside]
      (let [clouds (get-clouds outside)]
        (.getType clouds)))

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

`Clouds.prototype.frog` does not exist in externs

!SLIDE

Change ` (.frog clouds)` to `(.getType clouds)`

Add `frog` to `inferred_externs.js`

    @@@javascript
    Clouds.prototype.frog = function() {};

!SLIDE

# `cljs-oops`
# Sidestep externs entirely using string names

!SLIDE

## Idea Use `goog.object/get` or `aget`

    @@@clojure
    (ns my.app
     (:require [goog.object :as gobj]))

    (defn cloudy [outside]
      (.call (gobj/get outside "getClouds") outside))


!SLIDE

# `cljs-oops` provides macros for automation

!SLIDE

# `oget`
## Retrieve JavaScript Object properties

    @@@clojure
    (def home #js {"floor" #js {"living-room" "500 sqft"}})
    (oget home "floor" "living-room")
    ;; => "500 sqft"

!SLIDE

# `oset!`
## Set JavaScript Object properties

    @@@clojure
    (def home #js {"floor" #js {"living-room" "500 sqft"}})
    (oset! home  "floor" "living-room" "300 sqft")
    ;; => #js {"floor" #js {"living-room" "300 sqft"}}

!SLIDE

# `ocall`
## Call JavaScript methos with fixed arguments

    @@@clojure
    (def car #js {"ispy" (fn [desc item] (str "I see a " desc " " item))})
    (ocall car ["ispy"] "red" "barn")
    ;; => "I see a red car"

!SLIDE

# `oapply`
## Call JavaScript methos with variadic arguments

    @@@clojure
    (def bill #js {"total" (fn [& items] (reduce + items))})
    (oapply bill "total" [1 2 3])
    ;; => 6

!SLIDE

# `cljs-oops` not just automation

## extensive validation during development
## emits optimized code during advanced compilation

!SLIDE

# Navigating JavaScript Objects
## Access Modifiers

- `?` soft access, returns `nil` for non existent key
    - Change `key` to `?key`
- `!` punching, creates key when it does not exist
    - Change `key` to `!key`


!SLIDE

# `?` soft access
## Like `get-in`

        @@@clojure
        (def home #js {"house" #js {"bedroom" #js {:color "red"}}})
        (oget home "house" "?livingroom")
        ;; => nil

&nbsp;

        @@@clojure
        (def home {:house {:bedroom {:color "red"}}})
        (get-in home [:house :living-room])
        ;; => nil
!SLIDE

# `!` punching
## Like `assoc-in`

        @@@clojure
        (def home #js {})
        (oset! home "!house" "!livingroom" "!color" "green")
        ;; => #js {"house" #js {"livingroom" #js {"color" "green"}}}

&nbsp;

        @@@clojure
        (def home {})
        (assoc-in home [:house :livingroom :color] "green")
        ;; => {:house {:livingroom {:color "green"}}}

!SLIDE

# Use in small doses
## String Names defeats Google Closure Advanced Optimization

!SLIDE

## Opns up the JavaScript ecosystem
### Selectively use library features with

- ClojureScript externs inference
- `cljs-oops`

!SLIDE

# CLJSJS library is the best option
## Externs file next best options
## When neither exist use

- ClojureScript externs inference
- `cljs-oops`
