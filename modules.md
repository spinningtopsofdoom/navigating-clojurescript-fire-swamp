# Modules

## Lessons
- Very important for mobile slow phones leave fast
- What code gets moved around
- Dynamically loading modules (client side)

## Mobile Slow
3 seconds and 53% leave site average site load is 19 seconds
15% speed up by putting phone on ice
Processors spend most time in low power mode, SSD spinning rust due to form factor, network is incredibly slow

## JS vs images
Can't remove image and add JavaScript
JavaScript takes more time to parse and evaluate then images

## Google Modules
Split code into modules to progressively load site
Get first time to interactive much faster

## Code Splitting
How to split apart modules
What code can be moved by code motion?

## Dynamic Module Loading
Use Closure Library `goog.module`
https://rasterize.io/blog/cljs-dynamic-module-loading.html
How to make sure modules are loaded asynchronously
