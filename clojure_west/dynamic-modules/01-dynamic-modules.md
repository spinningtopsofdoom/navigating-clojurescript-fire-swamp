# ClojureScript Modules

!SLIDE
# ClojureScript Modules pretty straight forward

    @@@clojure

    :modules {:extra {:output-to "resources/modules/extra.js"
                      :entries #{"my.module.core"}}
              :dev   {:output-to "resources/modules/dev.js"
                      :entries #{"my.module.root"}
                      :depends-on #{:extra}}}

!SLIDE

# Google Closure takes care of hard work
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

# Desired Root Module

    @@@clojure
    (ns my.module.root)

    (load-module "extra" (fn [] (.log js/console "extra loaded")))

!SLIDE

# For development

    @@@clojure
    :preloads '[my.module.extra]


!SLIDE

## Loading Modules in Development
### Modules already auto loaded check every 100ms

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

## Choose with `goog-define`

    @@@clojure
    (goog-define PRODUCTION false)

    (def load-module
      (if ^boolean PRODUCTION
        load-module-prod
        load-module-dev))

!SLIDE

# It would be great ClojureScript library for this

!SLIDE

# There is
## `[conwip-modules 0.1.0]`
