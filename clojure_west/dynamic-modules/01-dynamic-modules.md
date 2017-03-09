# ClojureScript Modules

!SLIDE

## Splitting up application into modules increases performance
- Only load what is necessary
- Load modules in parallel

!SLIDE

## JavaScript build tools are just starting to do code splitting
### often requiring manual marking of split points

!SLIDE

## Google Closure has has this for years
- Plus can do code motion
## ClojureScript has had modules since `0.0-3178` (two years)

!SLIDE

Google Closure can only move

- Constant literals (e.g. `1`, `"cat"`, `true`)
- Functions
- Arrays or Objects of the two above values

!SLIDE

# Dynamic Module Loading
TODO flesh this out
