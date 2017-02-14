# Externs

## Lessons
- What they are
- difficulties with ClojureScript code
- Third Party Externs (cljsjs)
- Externs inference
- Bypassing with Google Object

## What are they
Google Closure Compiler is free to perform any transformations on source code it wants
Guide for telling Google Closure compiler not to modify this code
Any places that calling third party JavaScript needs externs

## How Implemented
For each third party library add a externs file
CLJSJS resource of libraries combined with externs

# Problems
Externs and code maintained by two separate people and not up to date
Third party works in development works just fine
Debugging is a very slow pain requiring a custom advanced compilation build

# Externs Inference
With new version (1.9.423) of ClojureScript externs can be inferred from ClojureScript Source code
You need to add a "type hint" to your ClojureScript code
You can also set warnings when you are using a JavaScript library
It will generate an externs file for you
Note functions will need the return type

## Avoid externs
Use Google Object module functionality and string names
`(.call (gobj/get js/D3 "foo") :bar)` instead of `(.foo js/D3 :bar)`
Always safe in every mode
`call` gets compiled away under advanced optimization

## Cljs-oops
Library that automates the above pattern
Tunable Safety checks during development
Fast when accessing non dynamic properties
Use with no Clsoure compatible code
