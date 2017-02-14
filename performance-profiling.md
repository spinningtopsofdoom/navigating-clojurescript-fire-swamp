# Performance Profiling

## Lessons
- Heuristics for fast ClojureScript
- What compiler options to choose and why
- Basic profiling with devtools
- Allocations
- Type conversion (arrays)
- Monomorphism
- Advanced JavaScript VM profiling

## Libraries
Browser devtools give a good indication on where problem sopts are
`(time (dotimes [_ 10000]) (my-fn))` good for quick and dirty work on repl
Use when making a library or confirmed code hotspot
Check Algorithm

## Compiler Options
- `:static-fns true` changes dynamic calling via `call()` to a static call `fn()`
- `:pretty-print true` prints generated code in more readable format
- `:optimizations :simple` does optimizations on the local level but not global code minimization that hurts code performance and readability

# Type Hint for Boolean

ClojureScript truth differs from JavaScript truth
only `nil` and `false` are false
If compiler can't infer truth then calls `cljs.core._truth` much slower
Grep for `cljs.core._truth` in generated `https://www.npmjs.com/package/sourcemap-lookup`

## Watch out for allocations
Allocations in hotspots add GC pressure, interferes with CPU caches
Preallocate buffer
Transductions for long pipelines

## Type Conversions
When a variable changes types during run time
When types stay constant compilers can perform more aggressive optimizations
Less bytecode checks

## Monomorphism
Call site cache assumes same shape during calls
JsObject and type with the same fields are different types
~2 different types polymorphic
~4 different types megamorphic

## Advanced JIT Profiling
IRHydra2 (for node, chrome, V8) visual profiling shows in source code where deoptimizations happen
JSC profile then ruby tool to query code gives reasons for optimizations and deoptimizations
