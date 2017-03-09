# Performance and profiling

!SLIDE

# To paraphrase a common saying

## Solve a developers performance problem and the code will run fast for a day

## Teach a developer a performance heuristic and it'll be applied indiscriminately everywhere forever

!SLIDE

# Performant JavaScript code is a slippery __mutable__ thing

!SLIDE

# Example: JavaScript Immutable Strings

    @@@javascript
    var result = ["this", "is", "slow"].join("");

&nbsp;

    @@@javascript
    var result = "this" + "is" + "fast";

!SLIDE

# The compiler options that strikes the best balance between readability and production quality

    @@@ clojure
    {;; Compile to production quality code
     :static-fns true
     :optimizations :simple
     ;; Debugging options
     :pretty-print true
     :source-map "out/app.js.map"
     :output-file "out/app.js"}

!SLIDE

# `:static-fns false`

    @@@javascript
    my.sample.app.square.call(null, 5)

## Re definable and reloadable functions

&nbsp;
# `:static-fns true`

    @@@javascript
    my.sample.app.square(5)

## Fast Direct functions invocation

!SLIDE

## `:optimizations simple`
&nbsp;
## `:none` and `:whitespace`selections do not perform any optimizations
&nbsp;
## `:advanced`  optimizations aggressive renaming makes for impenetrable JavaScript

!SLIDE

## `pretty-print true`
## makes the generated JavaScript code more readable
&nbsp;
## `:source-map "out/app.js.map"`
## allows you to map the profiled JavaScript code back to ClojureScript code


!SLIDE

# If you need to profile in advanced mode turn `:pseudo-names` on

    @@@ clojure
    {:pretty-print true
     :optimizations :advanced
     :pseudo-names true
     :source-map "app.js.map"
     :output-file "app.js"}

