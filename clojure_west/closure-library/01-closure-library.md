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
- `debounce`, `throttle`, `delay` with customizable Objects
- `DateTime` `UTCDateTime`, `Interval`, `URI`

!SLIDE

## `goog.array` and `goog.iter`are like Closure standard library for collections
`cycle`, `dropWhile`, `groupBy`, `reduce`, `every`

!SLIDE

# `goog.object` great for accessing and manipulating JavaScript objects
- Instead of using `aget` and `aset` use  `goog.object.get` and `goog.object.set`
- Gets compiled away with advanced compilation

!SLIDE

TODO maybe `goog.define`
