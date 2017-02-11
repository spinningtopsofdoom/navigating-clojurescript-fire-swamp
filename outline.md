# Navigaing ClojureScript's Fire Swamps

# High Level Goals
 - Give signpostsand concepts for ClojureScripts Advanced Features
 - Very Approachable / low barrier to entry
 - Smooth Transitions
 - Systemtize current tribal knowledge
 - Reference for people to come to
 - Give reasons for following advice
 
 # Important Concepts
 
 ## Profiling ClojureScript code
  - build with `{:optimizations :simple :static-fns true :pretty-print true :source-maps true}`
    - Removes dynamic calling, Optimization level is fastest not most compact
  - Make code as static (stable objects, monomorphic call sites) as possible reduce allocations
  - `^:boolean`type hint allows for major speedups
  - IRHydra gives locations where code is being deoptimized, jsc gives more detailed infromation
    
  
## Google Closure Library
  - Awesome but nearly undocumented, it should be be the first place to look for stand library
  - goog.functions have throttle, debounce, once
  - goog.Uri can parse and create Uri
  - Locale for internationalization
  - Old style AJAX and new fetch api
  - Interact with JavaScript through goog.object
    - Guaranteed Closure compatible instead on hacky JS interop
    - Help with advanced optimizations
  
# Externs
  - Worked witha library that copied `goog.math.Long`
    ```
    goog = {};
    goog.math = {};
    ```
    
  - Tells Google Closure how to work with third party JavaScript.
  - Advanced optimizations is free rename any word it sees fit, externs tell Google Closure "don't change this" (Think of a descriptive analogy for this)
  - Externs files are typically maintained by hand
  - only popular ones are well maintained and available via CLJSJS for everything else you're on your own.
  - Externs inference recently became available
    - You can have the compiler warn you when your using js that could be rewritten
    - By doing type hints (ala *warn on reflection*) you can have ClojureScript write outan externs file for you
    - Singleton JavaScript pattern (e.g. D3, jQuery)
    - Or avoid externs altogether (ala cljs-oops)
      - Idea is to call eveything via strings and goog.object functionaloty
      - Freindly facade to avoid boiler plate and advanced optimization freindly

# Google Modules
  - Sourcemap explorerhttps://github.com/danvk/source-map-explorer
  - Spltting ClojureScript code into seperate chunks
  - Done via namespace
  - What can be moved between namespaces?
  - How to load modules from clientside?
  - Needed for mobile, big JavaScript file breaks 3 second window. 53% of users quit after 3 seconds average mobile web page takes 19 seconds to load
  
  
# Third Party (Aka npm modules) loading
  - Bleeing edge
  - Just landed and requires lots of hoops to jump through
  - You need externs, module deps to get the needed source files, node extrens for any internal details not exposed.
  - Good news being pushed via angular to get fast modularized JavaScript \o/
  - Right now using commonjs but should be moving towards ES6 modules.
  - Gets React ~20kb gzip min lower than other solutions
  
# Dead code elimination
  - Mainly for library developers or common functionality
  - What is elimitaed (e.g. functions) and what remains (e.g. ClojureScript Maps)
  - See cljs.time
  
# Thanks for all resources and people used
