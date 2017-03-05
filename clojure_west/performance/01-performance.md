# Performance and profiling

!SLIDE

Optimizing without profiling is like trying to find iocane powder (TODO better anology)

!SLIDE

# We want to profile our application to be a close to the production app as possible while being readable

!SLIDE

# Sample application

    @@@@clojure
    (ns my.sample.app)

    (defn square [a]
      (* a a))

    (.log js/console (square 5))

!SLIDE

# The compiler options that strikes the best balance between readability and production quality

    @@@ clojure
    {:static-fns true
     :pretty-print true
     :optimizations :simple
     :source-map "out/app.js.map"
     :output-file "out/app.js"}

!SLIDE

# `:static-fns false` (reloadable)

    @@@javascript
    my.sample.app.square.call(null, 5)
&nbsp;
# `:static-fns true` (fast)

    @@@javascript
    my.sample.app.square(5)


!SLIDE

## `pretty-print true`
## makes the generated JavaScript code more readable
&nbsp;
## `:source-map "out/app.js.map"`
## allows you to map the profiled JavaScript code back to ClojureScript code

!SLIDE

## `:optimizations simple`
&nbsp;
## `:advanced`  optimizations aggressively minimizes code size often at the expense of performance
&nbsp;
## `:none` and `:whitespace`selections do not perform any optimizations

!SLIDE

# If you need to profile in advanced mode turn `:pseudo-names` on

    @@@ clojure
    {:pretty-print true
     :optimizations :advanced
     :pseudo-names true
     :source-map "app.js.map"
     :output-file "app.js"}

!SLIDE

# Typical performance bottlenecks
## Check these only in libraries and confirmed code hotspots

!SLIDE

## Clojure truth vs JavaScript truth

    @@@clojure
    (def count 5)
    (when count
      (.log js/console "beep boop")

&nbsp;

    @@@javascript
    var count = 5;
    if (cljs.core_truth(5)) {
        console.log("beep boop");
    }

`cljs.core._truth` checks if `count` is `nil` or `false`

!SLIDE

## The fix is simple use a boolean type hint

    @@@clojure
    (def count 5)
    (when ^:bool count
      (.log js/console "beep boop")

&nbsp;

    @@@javascript
    var count = 5;
    if (5) {
        console.log("beep boop");
    }

!SLIDE

# Over Allocation
## Commoner is ClojureScript than JavaScript due to immutability and sequence abstractions

## Downsides
- Triggers Garbage Collection more often
- Unfriendly towards CPU caches

!SLIDE

# Fixes
## Transducers
Removes intermediate collections in data pipelines
##Transients
Operations update in place rather then making many new objects per operation

!SLIDE

# JIT Profiling
TODO see if time allows
Warning going into pretty deep waters
IRHydra JIT bytecode example and using JavaScript Core profile
