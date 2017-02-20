# Performance
## Heuristics and profiling

!SLIDE

# For quick and dirty checks on the REPL

    @@@ clojure
    (time
      (dotimes [_ 1e5]
        (benchmark-fn)))

!SLIDE

# To profile whole program (e.g. with Chrome Dev tools)

    @@@ clojure
    {:static-fns true
     :pretty-print true
     :optimizations :simple
     :source-map "app.js.map"
     :output-file "app.js"}

!SLIDE

# `:static-fns true`
# turns off dynamic function redefinition that is used for function reloading at the REPL

!SLIDE

# `pretty-print true`
# makes the generated JavaScript code more readable

# `:source-map "out/app.js.map"`
# allows you to map the profiled JavaScript code back to ClojureScript code

!SLIDE

# `:optimizations simple`
# `:advanced`  optimizations does global optimizations for code size at the expense of speed. The renaming done for code shrinkage makes debugging considerably more difficult
# `:none` and `:whitespace` do not perform any optimizations (TODO exand here)

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
## Only check if you're making a library or have confirmed a hotspot through profiling

!SLIDE

# Too much Allocation
- More costly then updating in place
- Triggers Garbage Collection more often
- Unfriendly towards CPU caches

!SLIDE

# Change long data pipelines to transducers

    @@@@ clojure
    TODO write pipleline here

!SLIDE

# Use Transients instead of persistent collections

    @@@ clojure
    TODO example of transients

!SLIDE

# ClojureScript truth vs JavaScript truth
## In ClojureScript only `nil and `false` are false. Necessitates `cljs.core.truth_` function much slower then JavaScript truth check

!SLIDE

Help the compiler by adding a `^boolean` type hint where needed. You can check generated code for `cljs.core.truth`_ functions

!SLIDE

Warning going into pretty deep waters
IRHydra JIT bytecode example and using JavaScript Core profile
