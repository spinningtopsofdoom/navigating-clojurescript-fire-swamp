# Google Closure Library
## JavaScript Standard Library

!SLIDE

Google Closure Library comes packaged with ClojureScript

- Batteries Included Like other languages (e.g. Python, Ruby)
- Easily added to project
- Written for Google Closure Compiler

!SLIDE

## It's a great standard library
### Except for the documentation

!SLIDE

## Great buried gems

- AJAX classic and using new fetch API
- `debounce`, `throttle`, `delay` with customizable Objects
- `DateTime` `UTCDateTime`, `Interval`, `URI`

!SLIDE

# `goog.object`
- Robust interaction with JavaScript Objects
- Safe for every compilation choice
- Use  `goog.object.get` and `goog.object.set` instead of `aget` and `aset`

!SLIDE

## Parameterize builds with `goog.define`
### `goog-define`ClojureScript  wrapper function

!SLIDE

# Flags

    @@@clojure
    (ns my.setting)

    (goog-define QA false)

    (if ^boolean QA
      (.log js/console "QA")
      (.log js/console "Not QA")

## Compiler Setting

`clojure-defines {'my.setting.QA true}`

!SLIDE

# Complier setting overrides `goog.define`

!SLIDE

# Product Features

    @@@clojure
    (ns my.setting)

    (goog-define USER "normal")

    (if (identical? USER "admin")
      (.log js/console "Admin View"))

    (def oversees
      (cond
        (identical? USER "admin") #{"supervisors", "users"}
        (identical? USER "supervisor") #{"users"}
        #{}))

## `identical?`   needed for Dead Code elimination

!SLIDE

## `clojure-defines` alone won't work
## Namespace and `goog-define` are needed
### Gives Google Closure the necessary JavaScript
