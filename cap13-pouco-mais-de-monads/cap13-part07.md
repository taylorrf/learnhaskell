Making monads
=============

In this section, we're going to look at an example of how a type gets made, identified as a monad and then given the appropriate [code]Monad[/code] instance. We don't usually set out to make a monad with the sole purpose of making a monad. Instead, we usually make a type that whose purpose is to model an aspect of some problem and then later on if we see that the type represents a value with a context and can act like a monad, we give it a [code]Monad[/code] instance.

As we've seen, lists are used to represent non-deterministic values. A list like [code][3,5,9][/code] can be viewed as a single non-deterministic value that just can't decide what it's going to be. When we feed a list into a function with [code]&gt;&gt;=[/code], it just makes all the possible choices of taking an element from the list and applying the function to it and then presents those results in a list as well.

If we look at the list [code][3,5,9][/code] as the numbers [code]3[/code], [code]5[/code] and [code]9[/code] occurring at once, we might notice that there's no info regarding the probability that each of those numbers occurs. What if we wanted to model a non-deterministic value like [code][3,5,9][/code], but we wanted to express that [code]3[/code] has a 50% chance of happening and [code]5[/code] and [code]9[/code] both have a 25% chance of happening? Let's try and make this happen!

Let's say that every item in the list comes with another value, a probability of it happening. It might make sense to present this like this then:

In mathematics, probabilities aren't usually expressed in percentages, but rather in real numbers between a 0 and 1. A 0 means that there's no chance in hell for something to happen and a 1 means that it's happening for sure. Floating point numbers can get real messy real fast because they tend to lose precision, so Haskell offers us a data type for rational numbers that doesn't lose precision. That type is called [code]Rational[/code] and it lives in [code]Data.Ratio[/code]. To make a [code]Rational[/code], we write it as if it were a fraction. The numerator and the denominator are separated by a [code]%[/code]. Here are a few examples:

The first line is just one quarter. In the second line we add two halves to get a whole and in the third line we add one third with five quarters and get nineteen twelfths. So let'use throw out our floating points and use [code]Rational[/code] for our probabilities:

Okay, so [code]3[/code] has a one out of two chance of happening while [code]5[/code] and [code]9[/code] will happen one time out of four. Pretty neat.

We took lists and we added some extra context to them, so this represents values withs contexts too. Before we go any further, let's wrap this into a [code]newtype[/code] because something tells me we'll be making some instances.

Alright. Is this a functor? Well, the list is a functor, so this should probably be a functor as well, because we just added some stuff to the list. When we map a function over a list, we apply it to each element. Here, we'll apply it to each element as well, only we'll leave the probabilities as they are. Let's make an instance:

We unwrap it from the [code]newtype[/code] with pattern matching, apply the function [code]f[/code] to the values while keeping the probabilities as they are and then wrap it back up. Let's see if it works:

Another thing to note is that the probabilities should always add up to [code]1[/code]. If those are all the things that can happen, it doesn't make sense for the sum of their probabilities to be anything other than [code]1[/code]. A coin that lands tails 75% of the time and heads 50% of the time seems like it could only work in some other strange universe.

Now the big question, is this a monad? Given how the list is a monad, this looks like it should be a monad as well. First, let's think about [code]return[/code]. How does it work for lists? It takes a value and puts it in a singleton list. What about here? Well, since it's supposed to be a default minimal context, it should also make a singleton list. What about the probability? Well, [code]return x[/code] is supposed to make a monadic value that always presents [code]x[/code] as its result, so it doesn't make sense for the probability to be [code]0[/code]. If it always has to present it as its result, the probability should be [code]1[/code]!

What about [code]&gt;&gt;=[/code]? Seems kind of tricky, so let's make use of the fact that [code]m &gt;&gt;= f[/code] always equals [code]join (fmap f m)[/code] for monads and think about how we would flatten a probability list of probability lists. As an example, let's consider this list where there's a 25% chance that exactly one of [code]'a'[/code] or [code]'b'[/code] will happen. Both [code]'a'[/code] and [code]'b'[/code] are equally likely to occur. Also, there's a 75% chance that exactly one of [code]'c'[/code] or [code]'d'[/code] will happen. [code]'c'[/code] and [code]'d'[/code] are also equally likely to happen. Here's a picture of a probability list that models this scenario:

What are the chances for each of these letters to occur? If we were to draw this as just four boxes, each with a probability, what would those probabilities be? To find out, all we have to do is multiply each probability with all of probabilities that it contains. [code]'a'[/code] would occur one time out of eight, as would [code]'b'[/code], because if we multiply one half by one quarter we get one eighth. [code]'c'[/code] would happen three times out of eight because three quarters multiplied by one half is three eighths. [code]'d'[/code] would also happen three times out of eight. If we sum all the probabilities, they still add up to one.

Here's this situation expressed as a probability list:

Notice that its type is [code]Prob (Prob Char)[/code]. So now that we've figure out how to flatten a nested probability list, all we have to do is write the code for this and then we can write [code]&gt;&gt;=[/code] simply as [code]join (fmap f m)[/code] and we have ourselves a monad! So here's [code]flatten[/code], which we'll use because the name [code]join[/code] is already taken:

The function [code]multAll[/code] takes a tuple of probability list and a probability [code]p[/code] that comes with it and then multiplies every inner probability with [code]p[/code], returning a list of pairs of items and probabilities. We map [code]multAll[/code] over each pair in our nested probability list and then we just flatten the resulting nested list.

Now we have all that we need, we can write a [code]Monad[/code] instance!

Because we already did all the hard work, the instance is very simple. We also defined the [code]fail[/code] function, which is the same as it is for lists, so if there's a pattern match failure in a [code]do[/code]expression, a failure occurs within the context of a probability list.

It's also important to check if the monad laws hold for the monad that we just made. The first one says that [code]return x &gt;&gt;= f[/code] should be equal to [code]f x[/code]. A rigorous proof would be rather tedious, but we can see that if we put a value in a default context with [code]return[/code] and then [code]fmap[/code] a function over that and flatten the resulting probability list, every probability that results from the function would be multiplied by the [code]1%1[/code] probability that we made with [code]return[/code], so it wouldn't affect the context. The reasoning for [code]m &gt;&gt;= return[/code] being equal to just [code]m[/code] is similar. The third law states that [code]f &lt;=&lt; (g &lt;=&lt; h)[/code] should be the same as [code](f &lt;=&lt; g) &lt;=&lt; h[/code]. This one holds as well, because it holds for the list monad which forms the basis of the probability monad and because multiplication is associative. [code]1%2 * (1%3 * 1%5)[/code] is equal to [code](1%2 * 1%3) * 1%5[/code].

Now that we have a monad, what can we do with it? Well, it can help us do calculations with probabilities. We can treat probabilistic events as values with contexts and the probability monad will make sure that those probabilities get reflected in the probabilities of the final result.

Say we have two normal coins and one loaded coin that gets tails an astounding nine times out of ten and heads only one time out of ten. If we throw all the coins at once, what are the odds of all of them landing tails? First, let's make probability values for a normal coin flip and for a loaded one:

And finally, the coin throwing action:

Giving it a go, we see that the odds of all three landing tails are not that good, despite cheating with our loaded coin:

All three of them will land tails nine times out of forty, which is less than 25%. We see that our monad doesn't know how to join all of the [code]False[/code] outcomes where all coins don't land tails into one outcome. That's not a big problem, since writing a function to put all the same outcomes into one outcome is pretty easy and is left as an exercise to the reader (you!)

In this section, we went from having a question (what if lists also carried information about probability?) to making a type, recognizing a monad and finally making an instance and doing something with it. I think that's quite fetching! By now, we should have a pretty good grasp on monads and what they're about.