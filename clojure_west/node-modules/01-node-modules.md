# Compiling Node Modules

!SLIDE

Google Closure Compiler can compile node modules

- Facebook React and React DOM 53k
- Google Closure React and React DOM 32k

!SLIDE

# New compiler option `npm-deps`

    @@@clojure
    {:npm-deps {:react     "15.4.2"
                :react-dom "15.4.2"}}

!SLIDE

# `react` and `react-dom` are accessible directly in ClojureScript

    @@@clojure
    (ns my.app
      (:require [react :as React]
                [react-dom :as ReactDOM]))

    (def app (React/createElement "h1" nil "Hello World!"))
    (ReactDOM/render app (.getElementById js/document "app"))


!SLIDE

# React and ReactDom are namespaces

## Use like this

    @@@clojure
    (React/createElement "hi" nil "Hello World")

## CLJSJS Style

    @@@clojure
    (.createElement js/React "hi" nil "Hello World")

!SLIDE

# ClojureScript does not manage JavaScript dependency graph

## `rainbow`

- `red@1.4.5`
- `blue@3.0.0`

## `prism`

- `red@0.13.4`
- `blue@4.3.1`

## `paint`

- `red@02.0.4`
- `blue@4.5.0`

!SLIDE

## Application development is the big winner

- `npm` or `yarn` can manage JavaScript dependencies
- Maximize Dead Code Elimination

!SLIDE

# Now for caveats, addendum's, and jumping though hoops

!SLIDE

# `module-deps` library needed

- `npm install --save-dev module-deps`
- `yarn add --dev module-deps`

!SLIDE

## Externs for node needed

- JavaScript use node to compile JavaScript
- Available at https://github.com/dcodeIO/node.js-closure-compiler-externs

!SLIDE

# CLJSJS libraries for externs
## Tells Closure about dynamic (meta programmed) names

    @@@clojure
    [cljsjs/react "15.4.2-2"]
    [cljsjs/react-dom "15.4.2-2"]


!SLIDE

# Ho to make the miracle pill from scratch

!SLIDE

## How to make the miracle pill from scratch
- Install Dependencies through npm (install the universe)
- Create a JavaScript file detailing dependencies and exports (create a recipe)
- Run the file through `node-inputs` (fetch all the ingredient)
- Pass the results to ClojureScript through `foreign-libs` (create miracle pill)


!SLIDE

## To create a miracle pill from scratch first you must create the universe.

!SLIDE

# Install `react` and `react-dom`

## NPM
- `npm install --save react@15.4.2`
- `npm install --save react-dom@15.4.2`

## YARN
- `yarn add react@15.4.2`
- `yarn add  react-dom@15.4.2`

!SLIDE

# Create the recipe for the miracle pill

!SLIDE

Setup dependencies and exports

    @@@@javascript
    var React = require("react");
    var ReactDOMServer = require("react-dom");

    module.exports = {
      React: React,
      ReactDOM: ReactDOM
    };

!SLIDE

# Collect the ingredients for the miracle pill

!SLIDE

Pass in file as a `foreign-lib` to `cljs.closure/node-inpts`

    @@@clojure
    (require '[clojure.java.io :as io])
    (require 'cljs.closure)
    (require 'cljs.build.api)

    (def rppt-js-deps
      {:file (.getAbsolutePath (io/file "path/to/npm_deps.js"))
                       :provides ["libs.npm-deps"]
                       :module-type :commonjs})
    (def node-libs
      (into [entry] (cljs.closure/node-inputs [root-js-deps])))

!SLIDE

# Finally ready to make the miracle pill

!SLIDE


Pass `cljs.closure/node-inpts` result to `foreign-libs`

    @@@clojure
    (require 'cljs.build.api)

    (cljs.build.api/build "src"
      {:optimizations :advanced
       :output-to "out/app.js"
       :foreign-libs  node-libs})

!SLIDE

# Same result as `:npm-deps`
## `React` and `ReactDom` are in `lib-npm-deps`

    @@@clojure
    (ns my.app
      (:require [lib.npm-deps :as npm-deps]))


    (def app (npm-deps/React.createElement "h1" nil "Hello World!"))
    (npm-deps/ReactDOM.render app (.getElementById js/document "app"))

!SLIDE

# Still very alpha
## It's not just ClojureScript that is working at this

!SLIDE

# Major players integrating Google Closure

## React Fiber
https://github.com/facebook/react/issues/7925

## Angular Offline Template Compilation
https://github.com/angular/angular/issues/8550

## Typescript Compiler to Google Closure
https://github.com/angular/tsickle
