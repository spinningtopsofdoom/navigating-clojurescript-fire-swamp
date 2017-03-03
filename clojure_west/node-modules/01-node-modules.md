# Building Node Modules

!SLIDE

Node modules can now be consumed and compiled by the Google Closure Compiler
Current production React and React DOM 53k
With ClojureScript via Closure it's 32k

!SLIDE

Use case
- Application development to maximize Dead Code Elimination from common dependencies
- Wrapping very common JavaScript libraries

Very primitive version conflict resolution for the foreseeable future

!SLIDE

`module-deps` library needed to resolve node module dependencies

`npm install --save-dev module-deps`
`yarn add --dev module-deps`

!SLIDE

New compiler options `npm-deps`

    @@@clojure
    {:npm-deps {:react     "15.4.2"
                :react-dom "15.4.2"}}

!SLIDE

    @@@clojure
    (ns my.app
      (:require [react :as React]
                [react-dom :as ReactDOM]))

    (def app (React/createElement "h1" nil "Hello World!"))
    (ReactDOM/render app (.getElementById js/document "app"))

`react` and `react-dom` are now namespaces not objects

!SLIDE

Manual wiring going on behind the scenes
Look behind the curtain. Feel free to ask questions at any time.

!SLIDE

Install `react` and `react-dom`

`npm install --save react@15.4.2`
`npm install --save react-dom@15.4.2`

!SLIDE

Create root JavaScript file for dependencies located in `src/libs/node-deps.js`

    @@@@javascript
    var React = require("react");
    var ReactDOMServer = require("react-dom/server");

    module.exports = {
      React: React,
      ReactDOMServer: ReactDOMServer
    };

NPM libraries are now in single file

!SLIDE

Run `cljs.closure/node-inputs` over files

    @@@clojure
    (require '[clojure.java.io :as io]
             '[cljs.closure :as cc])

    (def node-libs
      (let [entry {:file (.getAbsolutePath (io/file "src/libs/npm_deps.js"))
                   :provides ["libs.npm-deps"]
                   :module-type :commonjs}]
        (into [entry] (cc/node-inputs [entry]))))

    {:foreign-libs node-libs}

Gives us `libs.npm-deps` namespace

!SLIDE

Now we can access `react-dom` server functionality under `ReactDOMServer`

    @@@clojure
    (ns my.app
      (:require [lib.npm-deps :as npm-deps]))

    (def app (npm-deps/React.createElement "h1" nil "Hello World!"))
    (npm-deps/ReactDOMServer.renderToString app)

!SLIDE
Not all rainbows yet

- You need the libraries externs
- plus any node externs used in build tooling

!SLIDE

Currently very alpha and involved build process

Check all libraries listed under `npm-deps`
Write file with `require` of node module dependencies
Shell out to node and run `module-deps` on file
`module-deps` gets all packages with associated files
ClojureScript processed them into `foreign-libs` with  `commonjs` `module-type`s
Update `foreign-libs` with processed node modules

!SLIDE

That just to process `node_modules` into a format consumable by ClojureScript. There's still the Google Closure Compiler that needs to process the modules.

Externs are needed for libraries
Due to how modern JavaScript libraries are built node externs (e.g. for `process`) may be needed as well.

!SLIDE

So are we hopeless outmached (TODO to the pain joke?)

No Google via Angular is working on integrating Google Closure into their product and looking at converting TypeScript to Google Closure compatible code `tsickle`

React team looking for a way to compile with Closure

It's a long road ahead but its not just ClojureScript pushing this
