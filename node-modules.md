# Compiling Node Modules

## Lessons
- Bleeding Edge
- Requires Externs
- Make a JavaScript file containing deps
- Needs tooling to find files and get dep graph
- Effort from Google  work (Angular TypeScript)
- Great gains (50 kb vs 35 kb react)

## Compile Node Modules
Much smaller final code size
Dead code elimination only pay for features you use
Better Code splitting?

## Tool Chain to make work
Need JavaScript file `require`ing third party dependencies
Need JavaScript Lib `module-deps`
Externs for Third Party JavaScript libraries
`psudoe-names` and `pretty-print` hunt for externs that weren't included

## Google Working on it
Not Only ScalaJs and ClojureScript who are working on this
Google is via TypeScript and Angular
Very early stages
