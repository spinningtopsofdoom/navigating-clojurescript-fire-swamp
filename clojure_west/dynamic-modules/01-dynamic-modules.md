# ClojureScript Modules

!SLIDE

## Splitting up application into modules increases performance
- Only load what is necessary
- Load modules in parallel

!SLIDE

# For splittable code

!SLIDE

# Dynamically Load ClojureScript Modules
## `goog.module.Manager` OOP boiler plate

!SLIDE
# Managing Modules Client Side

    @@@clojure

    :modules {:extra {:output-to "resources/modules/extra.js"
                      :entries #{"my.module.core"}}
              :dev   {:output-to   "resources/modules/dev.js"
                      :entries #{"my.module.root"}
                      :depends-on #{:extra}}}

!SLIDE

# Module Management OO Style

    @@@clojure
    (def modules
     ;; id -> urls
      #js {"extra" "resources/modules/extra.js"})

    (def module-info
      ;; id-> dependencies
      #js {"extra" #js []})

    (def manager (.getInstance goog.module.ModuleManager))
    (def loader (goog.module.ModuleLoader.))

    (.setLoader manager loader)
    (.setAllModuleInfo manager module-info)
    (.setModuleUris manager modules)

!SLIDE

# Mark Module as Loaded

    @@@clojure
    (.setLoaded (.getInstance goog.module.ModuleManager) "my.module.name")

!SLIDE

# Root Module
## `code.modules.extra` is eliminated in production

    @@@clojure
    (ns my.module.root
      (:require [my.module.extra]))

    (load-module "extra" (fn [] (.log js/console "extra loaded")))

!SLIDE

## Loading Modules in Development
### Auto loaded check every 100ms

    @@@clojure
    (defn load-module-dev [id callback]
      (let [interval (goog.Timer. 100)
            tick-fn (fn [_]
                      (when (loaded? id)
                        (.stop interval)
                        (goog.events/removeAll interval)
                        (callback)))]
        (goog.events/listen interval "tick" tick-fn)
        (.start interval)))

!SLIDE

## Loading Modules in Production

    @@@clojure
    (defn load-module-prod [id callback]
      (.execOnLoad (.getInstance goog.module.ModuleManager) id callback))


!SLIDE

## Choose with `goog-define`

    @@@clojure
    (goog-define PRODUCTION false)

    (def load-module
      (if ^boolean PRODUCTION
        load-module-prod
        load-module-dev))

!SLIDE

## Code Motion only works for
- Constant literals (e.g. `1`, `"cat"`, `true`)
- Functions and methods
- Arrays or Objects of the two above values

!SLIDE

    @@@clojure
    (ns my.module.shared)

    (def data #js {})

    (defmulti route #(:type %))

&nbsp;

    @@@clojure
    (ns my.modules.extra
      (:require [my.module.shared :as shared]
                [goog.object as :gobj])

    (gobj/set shared/description "extra" "an extra module")

    (defmethod shared/route :extra [info] {:route "/extra")
