# GHCi on Steroids

This shell script collection for configuring GHCi on the fly was
written by Sampsa "Tuplanolla" Kiiskinen between 2016-09-21 and 2016-09-23.
Look inside the files `ghci-on-steroids` and `example`
to see how the scripts are meant to be used.

## GoS Check

This little `gos-check` script checks your configuration file and
reports all the errors and ambiguities it contains.

    $ gos-check
    $ gos-check ~/.ghci

Note that `gos-check` depends on `prepare-gos-check`,
but is unrelated to the `gos-packages` and `gos-tags` scripts.

## GoS Packages and GoS Tags

These slightly bigger scripts `gos-packages` and `gos-tags`
produce configuration files from your modified configuration files
(configuration files on steroids)
that may contain commented package imports or custom tags.

    $ gos-packages
    $ gos-packages ~/.ghci-on-steroids
    $ gos-packages ~/.ghci ~/.ghci-on-steroids
    $ gos-packages -- package-1 ... package-$n
    $ gos-packages ~/.ghci-on-steroids -- package-1 ... package-$n
    $ gos-packages ~/.ghci ~/.ghci-on-steroids -- package-1 ... package-$n
    $ gos-tags
    $ gos-tags ~/.ghci-on-steroids
    $ gos-tags ~/.ghci ~/.ghci-on-steroids
    $ gos-tags -- tag-1 ... tag-$n
    $ gos-tags ~/.ghci-on-steroids -- tag-1 ... tag-$n
    $ gos-tags ~/.ghci ~/.ghci-on-steroids -- tag-1 ... tag-$n

For example `gos-packages vector` looks for lines of the form

    -- import "vector" Data.Vector as Vector (Vector)
    -- import qualified "vector" Data.Vector as Vector
    -- import "data-fix" Data.Fix as Fix

and uncomments the matches.

    import "vector" Data.Vector as Vector (Vector)
    import qualified "vector" Data.Vector as Vector
    -- import "data-fix" Data.Fix as Fix

Similarly `gos-tags basic` looks for lines of the form

    -- <types> <basic> import "vector" Data.Vector as Vector (Vector)
    -- <basic> import qualified "vector" Data.Vector as Vector
    -- <extra> import "data-fix" Data.Fix as Fix

and uncomments and untags the matches.

    import "vector" Data.Vector as Vector (Vector)
    import qualified "vector" Data.Vector as Vector
    -- <extra> import "data-fix" Data.Fix as Fix

Note that, as a safety measure, neither `gos-packages` nor `gos-tags`
overwrite your configuration file unless it starts with the following line.

    -- -*- generator: ghci-on-steroids -*-

## GHCi with GoS Packages or GoS Tags

These trivial scripts `ghci-with-packages` and `ghci-with-tags`
simply process your modified configuration files
(configuration files on steroids) and launch GHCi afterwards.

    $ ghci-with-packages package-1 ... package-$n
    $ ghci-with-tags tag-1 ... tag-$n

## Internals

Composing the internal procedures is possible.

    $ prepare-gos-check < ~/.ghci > GHCIOnSteroids.hs
    $ prepare-gos-packages package-1 ... package-$n < ~/.ghci-on-steroids > ~/.ghci
    $ prepare-gos-tags tag-1 ... tag-$n < ~/.ghci-on-steroids > ~/.ghci

## Known Issues

Both `gos-packages` and `gos-tags`
require each import or tagged thing to occupy exactly one line,
because they are implemented with line-oriented core utilities.
Such is life with shell scripts.
