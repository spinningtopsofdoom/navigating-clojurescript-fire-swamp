# Google Closure Library
## JavaScript Standard Library

!SLIDE

![Storming the Castle](../../images/castle.jpg)

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

- AJAX
    - classic `goog.net.XhrIo`
    - New fetch API `goog.net.FetchXmlHttp`
- `goog.async` Namespace
    - `Debouncer`, `Throttle`, `Delay`
- `goog.date` Namespace
    - `DateTime`, `Interval`, `Date`, `DateRange`
- JavaScript Collections
    - `goog.array`, `goog.iter`

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

# Boolean Feature Flag

    @@@clojure
    (ns my.setting)

    (goog-define ADMIN false)

    (def permisssions
      ;; ^boolean for dead code elimination
      (if ^boolean ADMIN
        {:access :all}
        {:access :user}

!SLIDE

# Enum (String) Feature Flag

    @@@clojure
    (ns my.setting)

    (goog-define USER "normal")

    ;; identical? for dead code elimination
    (def permisssions
      (if (identical? USER "admin")
        {:access :all}
        {:access :user}

    ;; cond and identical? for dead code elimination
    (def oversees
      (cond
        (identical? USER "admin") #{"supervisors", "users"}
        (identical? USER "supervisor") #{"users"}
        #{}))
