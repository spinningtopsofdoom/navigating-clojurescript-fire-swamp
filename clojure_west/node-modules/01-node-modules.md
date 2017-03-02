# Building Node Modules

!SLIDE

Node modules can now be consumed and compiled by the Google Closure Compiler
Current production React and React DOM 53k
With ClojureScript via Closure it's 32k

!SLIDE

Not all rainbows yet

- Node tooling needed `module-deps`, `JSONSTream`
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

So are will hopeless outmached (TODO to the pain joke?)

No Google via Angular is working on integrating Google Closure into their product and looking at converting TypeScript to Google Closure compatible code `tsickle`

React team looking for a way to compile with Closure

It's a long road ahead but its not just ClojureScript pushing this
