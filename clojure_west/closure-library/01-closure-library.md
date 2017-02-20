# Google Closure Library
## JavaScript Standard Library

!SLIDE

Google Closure Library comes packaged with ClojureScript
- Batteries Included Like other languages (e.g. Python, Ruby)
- Easily added to project
- Code is easily optimized by Google Closure Compiler

!SLIDE

It has all the functionality you could want in a standard library, except for documentation. It takes some digging around to find the necessary functionality

!SLIDE

It's worth looking around before searching for third party solution
- AJAX classic and using new fetch API
- Debounce, throttle, delay, once
- URI parsing and building

!SLIDE

# `goog.object` great for accessing and manipulating JavaScript objects
- Safer counterparts `goog.object.get` to `aget` and `aset` to `goog.object.set`
- Gets compiled away with advanced compilation

!SLIDE

# `goog.array` all the functionality you could want for array
Methods for manipulating a sorted array (e.g. `binaryInsert`, `binaryRemove`)
Several analogues of ClojureScript functions `repeat`, `map`, `concat`

!SLIDE

# `goog.iter` useful for dealing with JavaScript iterators
About as much functionality as ClojureScript standard library has for sequences
`cycle`, `dropWhile`, `groupBy`, `reduce`, `every`
