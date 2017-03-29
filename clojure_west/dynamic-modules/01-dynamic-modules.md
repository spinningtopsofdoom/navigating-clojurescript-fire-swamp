# Dynamically Loading ClojureScript Modules

!SLIDE
# ClojureScript Modules pretty straight forward

    @@@clojure

    :modules {:extra {:output-to "resources/modules/extra.js"
                      :entries #{"my.module.core"}}
              :dev   {:output-to "resources/modules/dev.js"
                      :entries #{"my.module.root"}
                      :depends-on #{:extra}}}

!SLIDE

# Google Closure does the hard work
## Cross Module Code Motion


!SLIDE

# Dynamically Loading ClojureScript Modules
## Morass of OOP boiler plate

!SLIDE

# Module Management OO Style

    @@@clojure
    ;; Singleton Module Manager
    (def manager (.getInstance goog.module.ModuleManager))

    (def loader (goog.module.ModuleLoader.))
    (.setLoader manager loader)

    (def modules
     ;; id -> urls
      #js {"extra" "resources/modules/extra.js"})

    (def module-info
      ;; id-> dependencies
      #js {"extra" #js []})
    (.setAllModuleInfo manager module-info)
    (.setModuleUris manager modules)

!SLIDE

# Mark Module as Loaded

    @@@clojure
    (ns my.module.name)

    (.setLoaded (.getInstance goog.module.ModuleManager) "my.module.name")

!SLIDE

# Just for Module Manager bookkeeping
## Modules still need to get dynamically loaded

!SLIDE

# Desired End Result

    @@@clojure
    (ns my.module.root)

    (load-module "extra" (fn [] (.log js/console "extra loaded")))

!SLIDE

# Development

    @@@clojure
    :optimizations :none
## `:modules` is not available
## All namespaces are auto loaded

!SLIDE

# Put all module namespaces in `:preloads`

    @@@clojure
    :preloads '[my.module.extra]

# Without `:preloads`


    @@@clojure
    (ns my.module.root
      (:require [my.module.extra]))

    (load-module "extra" (fn [] (.log js/console "extra loaded")))

!SLIDE

## Loading Modules in Development
### Check every 100ms if module has been auto loaded

    @@@clojure
    (defn load-module-dev [id callback]
      (let [interval (goog.Timer. 100)
            manager (.getInstance goog.module.ModuleManager)
            loaded? (fn []
                      (if-let [module (.getModuleInfo manager id)]
                        (.isLoaded module)
                        false))
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

## Choose module loader with `goog-define`

    @@@clojure
    (goog-define PRODUCTION false)

    (def load-module
      (if ^boolean PRODUCTION
        load-module-prod
        load-module-dev))

!SLIDE

# It would be great ClojureScript library for this

!SLIDE

# `conwip.modules`
## Currently removes about 50% of the boilerplate
## https://github.com/bendyworks/conwip-modules
