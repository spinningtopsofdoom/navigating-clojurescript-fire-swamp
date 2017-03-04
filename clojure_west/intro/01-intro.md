# Navigating ClojureScript's fire swamps

## @spinningtopsofdoom

## @bendyworks

!SLIDE

### Ground to Cover

- Profiling and performance
- Google Closure Library
- ClojureScript modules
- Externs
- Loading Node Modules

### As of ClojureScript `1.9.494`

!SLIDE

ClojureScript development and production ahead of typical JavaScript tool chain

Sub second transpilation with hot reloading

"Inconceivable"

Sub second generation of Fully accurate source maps

"Inconceivable"

Advanced Dead Code elimination and code splitting right out of the box

"Inconceivable"

!SLIDE

We're feeling pretty good about ourselves

!SLIDE

Then you encounter your first `ro.us`

    @@@javascript
    ro.us()
    "ro.us is not a function"

and spend hours hunting for a bug in externs
