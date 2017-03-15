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

# `goog.object`
- Safe robust interaction with JavaScript Objects
- Use  `goog.object.get` and `goog.object.set` instead of `aget` and `aset`

!SLIDE

## Parameterize builds with `goog-define`

    @@@clojure
    (ns my.setting)

    (goog-define LOG false)

!SLIDE

# Override `goog-define` wtih `closure-defines`
## Complier Setting

    @@@clojure
    :clojure-defines {'my.setting.LOG true}

!SLIDE

# Development vs Production settings

    @@@clojure
    (ns my.api)

    (goog-define TIMEOUT 300)

    (defn load-settings []
      (ajax-call {:imeout TIMEOUT}))

&nbsp;

    @@@clojure
    :clojure-defines {'my.api.TIMEOUT 5000}


!SLIDE

# Feature Flags
## Advanced Compilation removes unused features

!SLIDE

# Boolean Flag
## `^boolean` type hint for advanced compilation

    @@@clojure
    (ns my.setting)

    (goog-define ADMIN false)

    (def permisssions
      (if ^boolean ADMIN
        {:access :all}
        {:access :user}

!SLIDE

# Enum Flag
## `identical?` for advanced compilation

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
