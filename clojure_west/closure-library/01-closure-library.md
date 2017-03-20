# Google Closure Library
## JavaScript Standard Library

!SLIDE

![Storming the Castle](../../images/castle.jpg)

!SLIDE

# Google Closure Library

- Equivalent to Ruby or Python Standard Libraries
- Packaged with ClojureScript
- Written for Google Closure Compiler

!SLIDE

# It's a great standard library
## Except for the documentation

!SLIDE

## Great buried gems

- AJAX
    - classic `goog.net.XhrIo`
    - New fetch API `goog.net.FetchXmlHttp`
- `goog.async` Namespace
    - `Debouncer`, `Throttle`, `Delay`
- `goog.date` Namespace
    - `DateTime`, `Interval`, `Date`, `DateRange`

!SLIDE

# `goog.object` Namespace
- Safe and robust interaction with JavaScript Objects
- Use  `goog.object/get` and `goog.object/set` instead of `aget` and `aset`

!SLIDE

# `goog.define`
## Very well hidden
## Parameterize builds

!SLIDE

## Call with `goog-define` macro

    @@@clojure
    (ns my.api)

    (goog-define TIMEOUT 300)

    (defn load-settings []
      (ajax-call {:timeout TIMEOUT}))

## Override with `closure-defines` Complier Setting

    @@@clojure
    :clojure-defines {'my.api.TIMEOUT 5000}


!SLIDE

# Feature Flags
## Unused features removed via Dead Code Elimination

!SLIDE

# Boolean Values
## `^boolean` type hint

    @@@clojure
    (ns my.setting)

    (goog-define ADMIN false)

    (def permisssions
      (if ^boolean ADMIN
        {:access :all}
        {:access :user}

!SLIDE

# String Values
## `if` or `cond` conditional expressions
## `identical?` for comparison

    @@@clojure
    (ns my.setting)

    (goog-define USER "normal")

    (def permisssions
      (if (identical? USER "admin")
        {:access :all}
        {:access :user}

    (def oversees
      (cond
        (identical? USER "admin") #{"supervisors", "users"}
        (identical? USER "supervisor") #{"users"}
        #{}))
