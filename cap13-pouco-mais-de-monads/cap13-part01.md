For a Few Monads More
=====================

We've seen how monads can be used to take values with contexts and apply them to functions and how using [code]&gt;&gt;=[/code] or [code]do[/code] notation allows us to focus on the values themselves while the context gets handled for us. 

We've met the [code]Maybe[/code] monad and seen how it adds a context of possible failure to values. We've learned about the list monad and saw how it lets us easily introduce non-determinism into our programs. We've also learned how to work in the [code]IO[/code] monad, even before we knew what a monad was!

In this chapter, we're going to learn about a few other monads. We'll see how they can make our programs clearer by letting us treat all sorts of values as monadic ones. Exploring a few monads more will also solidify our intuition for monads.

The monads that we'll be exploring are all part of the [code]mtl[/code] package. A Haskell package is a collection of modules. The [code]mtl[/code] package comes with the Haskell Platform, so you probably already have it. To check if you do, type [code]ghc-pkg list[/code] in the command-line. This will show which Haskell packages you have installed and one of them should be [code]mtl[/code], followed by a version number.