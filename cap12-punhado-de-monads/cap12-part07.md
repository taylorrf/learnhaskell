Monad laws
==========

Just like applicative functors, and functors before them, monads come with a few laws that all monad instances must abide by. Just because something is made an instance of the [code]Monad[/code] type class doesn't mean that it's a monad, it just means that it was made an instance of a type class. For a type to truly be a monad, the monad laws must hold for that type. These laws allow us to make reasonable assumptions about the type and its behavior.

Haskell allows any type to be an instance of any type class as long as the types check out. It can't check if the monad laws hold for a type though, so if we're making a new instance of the [code]Monad[/code] type class, we have to be reasonably sure that all is well with the monad laws for that type. We can rely on the types that come with the standard library to satisfy the laws, but later when we go about making our own monads, we're going to have to manually check the if the laws hold. But don't worry, they're not complicated.


Left identity

The first monad law states that if we take a value, put it in a default context with [code]return[/code] and then feed it to a function by using [code]&gt;&gt;=[/code], it's the same as just taking the value and applying the function to it. To put it formally:

- [law]return x &gt;&gt;= f[/code] is the same damn thing as [law]f x[/code]

If you look at monadic values as values with a context and [code]return[/code] as taking a value and putting it in a default minimal context that still presents that value as its result, it makes sense, because if that context is really minimal, feeding this monadic value to a function shouldn't be much different than just applying the function to the normal value, and indeed it isn't different at all.

For the [code]Maybe[/code] monad [code]return[/code] is defined as [code]Just[/code]. The [code]Maybe[/code]
monad is all about possible failure, and if we have a value and want to put it in such a context, it makes sense that we treat it as a successful computation because, well, we know what the value is. Here's some [code]return[/code] usage with [code]Maybe[/code]:

For the list monad [code]return[/code] puts something in a singleton list. The [code]&gt;&gt;=[/code] implementation for lists goes over all the values in the list and applies the function to them, but 
since there's only one value in a singleton list, it's the same as applying the function to that value:

We said that for [code]IO[/code], using [code]return[/code] makes an I/O action that has no side-effects but just presents a value as its result. So it makes sense that this law holds for [code]IO[/code] as well.


Right identity

The second law states that if we have a monadic value and we use [code]&gt;&gt;=[/code] to feed it to [code]return[/code], the result is our original monadic value. Formally:

- [law]m &gt;&gt;= return[/code] is no different than 
    just [law]m[/code]

This one might be a bit less obvious than the first one, but let's take a look at why it should hold. When we feed monadic values to functions by using [code]&gt;&gt;=[/code], those functions take normal values and return monadic ones. [code]return[/code] is also one such function, if you consider its type. Like we said, [code]return[/code] puts a value in a minimal context that still presents that value as its result. This means that, for instance, for [code]Maybe[/code], it doesn't introduce any failure and for lists, it doesn't introduce any extra non-determinism. Here's a test run for a few monads:

If we take a closer look at the list example, the implementation for [code]&gt;&gt;=[/code] is:

So when we feed [code][1,2,3,4][/code] to [code]return[/code], first [code]return[/code] gets mapped over [code][1,2,3,4][/code], resulting in [code][[1],[2],[3],[4]][/code] and then this gets concatenated and we have our original list.

Left identity and right identity are basically laws that describe how [code]return[/code] should behave. It's an important function for making normal values into monadic ones and it wouldn't be good if the monadic value that it produced did a lot of other stuff.


Associativity

The final monad law says that when we have a chain of monadic function applications with [code]&gt;&gt;=[/code], it shouldn't matter how they're nested. Formally written:

- Doing [law](m &gt;&gt;= f) &gt;&gt;= g[/code] is just like doing [law]m &gt;&gt;= (\x -&gt; f x &gt;&gt;= g)[/code]

Hmmm, now what's going on here? We have one monadic value, [code]m[/code] and two monadic functions [code]f[/code] and [code]g[/code]. When we're doing [code](m &gt;&gt;= f) &gt;&gt;= g[/code], we're feeding [code]m[/code] to [code]f[/code], which results in a monadic value. Then, we feed that monadic value to [code]g[/code]. In the expression [code]m &gt;&gt;= (\x -&gt; f x &gt;&gt;= g)[/code], we take a monadic value and we feed it to a function that feeds the result of [code]f x[/code] to [code]g[/code]. It's not easy to see how those two are equal, so let's take a look at an example that makes this equality a bit clearer.

Remember when we had our tightrope walker Pierre walk a rope while birds landed on his balancing pole? To simulate birds landing on his balancing pole, we made a chain of several functions that might produce failure:

We started with [code]Just (0,0)[/code] and then bound that value to the next monadic function, [code]landRight 2[/code]. The result of that was another monadic value which got bound into the next monadic function, and so on. If we were to explicitly parenthesize this, we'd write:

But we can also write the routine like this:

[code]return (0,0)[/code] is the same as [code]Just (0,0)[/code] and when we feed it to the lambda, the [code]x[/code] becomes [code](0,0)[/code].

[code]landRight[/code] takes a number of birds and a pole (a tuple of numbers) and that's what it gets passed. This results in a [code]Just (0,2)[/code] and when we feed this to the next lambda, [code]y[/code] is [code](0,2)[/code]. This goes on until the final bird landing produces a [code]Just (2,4)[/code], which is indeed the result of the whole expression.

So it doesn't matter how you nest feeding values to monadic functions, what matters is their meaning. Here's another way to look at this law: consider composing two functions, [code]f[/code] and [code]g[/code]. Composing two functions is implemented like so:

If the type of [code]g[/code] is [code]a -&gt; b[/code] and the type of [code]f[/code] is [code]b -&gt; c[/code], we arrange them into a new function which has a type of [code]a -&gt; c[/code], so that its parameter is passed between those functions. Now what if those two functions were monadic, that is, what if the values they returned were monadic values? If we had a function of type [code]a -&gt; m b[/code], we couldn't just pass its result to a function of type [code]b -&gt; m c[/code], because that function accepts a normal [code]b[/code], not a monadic one. We could however, use [code]&gt;&gt;=[/code] to make that happen. So by using [code]&gt;&gt;=[/code], we can compose two monadic functions:

So now we can compose two monadic functions:

Cool. So what does that have to do with the associativity law? Well, when we look at the law as a law of compositions, it states that [law]f &lt;=&lt; (g &lt;=&lt; h)[/code] should be the same as [law](f &lt;=&lt; g) &lt;=&lt; h[/code]. This is just another way of saying that for monads, the nesting of operations shouldn't matter.

If we translate the first two laws to use [code]&lt;=&lt;[/code], then the left identity law states that for every monadic function [code]f[/code], [law]f &lt;=&lt; return[/code] is the same as writing just [law]f[/code] and the right identity law says that [law]return &lt;=&lt; f[/code] is also no different from [law]f[/code]. 

This is very similar to how if [code]f[/code] is a normal function, [code](f . g) . h[/code] is the same as [code]f . (g . h)[/code], [code]f . id[/code] is always the same as [code]f[/code] and [code]id . f[/code] is also just [code]f[/code].

In this chapter, we took a look at the basics of monads and learned how the [code]Maybe[/code] monad and the list monad work. In the next chapter, we'll take a look at a whole bunch of other cool monads and we'll also learn how to make our own.