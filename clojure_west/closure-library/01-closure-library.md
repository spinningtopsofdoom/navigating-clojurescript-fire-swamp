# Before storming the castle we need to list our assets

!SLIDE

![Storming the Castle](../../images/castle.jpg)

!SLIDE

# Google Closure Library

- JavaScript Standard Library (Batteries included)
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
- Safe robust interaction with JavaScript Objects
- Use  `goog.object.get` and `goog.object.set` instead of `aget` and `aset`

!SLIDE

## Parameterize builds through `goog.define`

- Different settings Development vs. Production
- Feature flags
- Unused features compiled away


!SLIDE

## Call with `goog-define` macro

    @@@clojure
    (ns my.setting)

    (goog-define LOG false)

## Override wtih `closure-defines` Complier Setting

    @@@clojure
    :clojure-defines {'my.setting.LOG true}

!SLIDE

# Development vs Production settings

    @@@clojure
    (ns my.api)

    (goog-define TIMEOUT 300)

    (defn load-settings []
      (ajax-call {:timeout TIMEOUT}))

&nbsp;

    @@@clojure
    :clojure-defines {'my.api.TIMEOUT 5000}


!SLIDE

# Feature Flags with Dead Code Elimination

!SLIDE

# Boolean
##  `^boolean` type hint

    @@@clojure
    (ns my.setting)

    (goog-define ADMIN false)

    (def permisssions
      (if ^boolean ADMIN
        {:access :all}
        {:access :user}

!SLIDE

# String
## `if` or `cond` with `identical?`

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
