Writer? I hardly know her!
==========================

We've loaded our gun with the [code]Maybe[/code] monad, the list monad and the [code]IO[/code] monad. Now let's put the [code]Writer[/code] monad in the chamber and see what happens when we fire it!

Whereas [code]Maybe[/code] is for values with an added context of failure and the list is for non-deterministic values, the [code]Writer[/code] monad is for values that have another value attached that acts as a sort of log value. [code]Writer[/code] allows us to do computations while making sure that all the log values are combined into one log value that then gets attached to the result.

For instance, we might want to equip our values with strings that explain what's going on, probably for debugging purposes. Consider a function that takes a number of bandits in a gang and tells us if that's a big gang or not. That's a very simple function:

Now, what if instead of just giving us a [code]True[/code] or [code]False[/code] value, we want it to also return a log string that says what it did? Well, we just make that string and return it along side our [code]Bool[/code]:

So now instead of just returning a [code]Bool[/code], we return a tuple where the first component of the tuple is the actual value and the second component is the string that accompanies that value. There's some added context to our value now. Let's give this a go:

So far so good. [code]isBigGang[/code] takes a normal value and returns a value with a context. As we've just seen, feeding it a normal value is not a problem. Now what if we already have a value that has a log string attached to it, such as [code](3, "Smallish gang.")[/code], and we want to feed it to [code]isBigGang[/code]? It seems like once again, we're faced with this question: if we have a function that takes a normal value and returns a value with a context, how do we take a value with a context and feed it to the function?

When we were exploring the [code]Maybe[/code] monad, we made a function [code]applyMaybe[/code], which took a [code]Maybe a[/code] value and a function of type [code]a -&gt; Maybe b[/code] and fed that [code]Maybe a[/code] value into the function, even though the function takes a normal [code]a[/code] instead of a [code]Maybe a[/code]. It did this by minding the context that comes with [code]Maybe a[/code] values, which is that they are values with possible failure. But inside the [code]a -&gt; Maybe b[/code] function, we were able to treat that value as just a normal value, because [code]applyMaybe[/code] (which later became [code]&gt;&gt;=[/code]) took care of checking if it was a [code]Nothing[/code] or a [code]Just[/code] value.

In the same vein, let's make a function that takes a value with an attached log, that is, an [code](a,String)[/code] value and a function of type [code]a -&gt; (b,String)[/code] and feeds that value into the function. We'll call it [code]applyLog[/code]. But because an [code](a,String)[/code] value doesn't carry with it a context of possible failure, but rather a context of an additional log value, [code]applyLog[/code] is going to make sure that the log of the original value isn't lost, but is joined together with the log of the value that results from the function. Here's the implementation of [code]applyLog[/code]:

When we have a value with a context and we want to feed it to a function, we usually try to separate the actual value from the context and then try to apply the function to the value and then see that the context is taken care of. In the [code]Maybe[/code] monad, we checked if the value was a [code]Just x[/code] and if it was, we took that [code]x[/code] and applied the function to it. In this case, it's very easy to find the actual value, because we're dealing with a pair where one component is the value and the other a log. So first we just take the value, which is [code]x[/code] and we apply the function [code]f[/code] to it. We get a pair of [code](y,newLog)[/code], where

[code]y[/code] is the new result and [code]newLog[/code] the new log. But if we returned that as the result, the old log value wouldn't be included in the result, so we return a pair of [code](y,log ++ newLog)[/code]. We use [code]++[/code] to append the new log to the old one.

Here's [code]applyLog[/code] in action:

The results are similar to before, only now the number of people in the gang had its accompanying log and it got included in the result log. Here are a few more examples of using [code]applyLog[/code]:

See how inside the lambda, [code]x[/code] is just a normal string and not a tuple and how [code]applyLog[/code] takes care of appending the logs.


Monoids to the rescue

Be sure you know what <a href="functors-applicative-functors-and-monoids#monoids">monoids</a> are at this point! Cheers.

Right now, [code]applyLog[/code] takes values of type [code](a,String)[/code], but is there a reason that the log has to be a [code]String[/code]? It uses [code]++[/code] to append the logs, so wouldn't this work on any kind of list, not just a list of characters? Sure it would. We can go ahead and change its type to this:

Now, the log is a list. The type of values contained in the list has to be the same for the original list as well as for the list that the function returns, otherwise we wouldn't be able to use [code]++[/code] to stick them together.

Would this work for bytestrings? There's no reason it shouldn't. However, the type we have now only works for lists. It seems like we'd have to make a separate [code]applyLog[/code] for bytestrings. But wait! Both lists and bytestrings are monoids. As such, they are both instances of the [code]Monoid[/code] type class, which means that they implement the [code]mappend[/code] function. And for both lists and bytestrings, [code]mappend[/code] is for appending. Watch:

Cool! Now our [code]applyLog[/code] can work for any monoid. We have to change the type to reflect this, as well as the implementation, because we have to change [code]++[/code] to [code]mappend[/code]:

Because the accompanying value can now be any monoid value, we no longer have to think of the tuple as a value and a log, but now we can think of it as a value with an accompanying monoid value. For instance, we can have a tuple that has an item name and an item price as the monoid value. We just use the [code]Sum[/code] newtype to make sure that the prices get added as we operate with the items. Here's a function that adds drink to some cowboy food:

We use strings to represent foods and an [code]Int[/code] in a [code]Sum[/code] [code]newtype[/code] wrapper to keep track of how many cents something costs. Just a reminder, doing [code]mappend[/code] with [code]Sum[/code] results in the wrapped values getting added together:

The [code]addDrink[/code] function is pretty simple. If we're eating beans, it returns [code]"milk"[/code] along with [code]Sum 25[/code], so 25 cents wrapped in [code]Sum[/code]. If we're eating jerky we drink whiskey and if we're eating anything else we drink beer. Just normally applying this function to a food wouldn't be terribly interesting right now, but using [code]applyLog[/code] to feed a food that comes with a price itself into this function is interesting:

Milk costs [code]25[/code] cents, but if we eat it with beans that cost [code]10[/code] cents, we'll end up paying [code]35[/code] cents. Now it's clear how the attached value doesn't always have to be a log, it can be any monoid value and how two such values are combined into one depends on the monoid. When we were doing logs, they got appended, but now, the numbers are being added up.

Because the value that [code]addDrink[/code] returns is a tuple of type [code](Food,Price)[/code], we can feed that result to [code]addDrink[/code] again, so that it tells us what we should drink along with our drink and how much that will cost us. Let's give it a shot:

Adding a drink to some dog meat results in a beer and an additional [code]30[/code] cents, so [code]("beer", Sum 35)[/code].And if we use [code]applyLog[/code] to feed that to [code]addDrink[/code], we get another beer and the result is [code]("beer", Sum 65)[/code].


The Writer type

Now that we've seen that a value with an attached monoid acts like a monadic value, let's examine the [code]Monad[/code] instance for types of such values.  The [code]Control.Monad.Writer[/code] module exports the [code]Writer w a[/code] type along with its [code]Monad[/code] instance and some useful functions for dealing with values of this type.

First, let's examine the type itself. To attach a monoid to a value, we just need to put them together in a tuple. The [code]Writer w a[/code] type is just a [code]newtype[/code] wrapper for this. Its definition is very simple:

It's wrapped in a [code]newtype[/code] so that it can be made an instance of [code]Monad[/code] and that its type is separate from a normal tuple. The [code]a[/code] type parameter represents the type of the value and the [code]w[/code] type parameter the type of the attached monoid value.

Its [code]Monad[/code] instance is defined like so:

First off, let's examine [code]&gt;&gt;=[/code]. Its implementation is essentially the same as [code]applyLog[/code], only now that our tuple is wrapped in the [code]Writer[/code] [code]newtype[/code], we have to unwrap it when pattern matching. 

We take the value [code]x[/code] and apply the function [code]f[/code] to it. This gives us a [code]Writer w a[/code] value and we use a [code]let[/code] expression to pattern match on it. We present [code]y[/code] as the new result and use [code]mappend[/code] to combine the old monoid value with the new one. We pack that up with the result value in a tuple and then wrap that with the [code]Writer[/code] constructor so that our result is a [code]Writer[/code] value instead of just an unwrapped tuple.

So, what about [code]return[/code]? It has to take a value and put it in a default minimal context that still presents that value as the result. So what would such a context be for [code]Writer[/code] values? If we want the accompanying monoid value to affect other monoid values as little as possible, it makes sense to use [code]mempty[/code]. [code]mempty[/code] is used to present identity monoid values, such as[code]""[/code] and [code]Sum 0[/code] and empty bytestrings. Whenever we use [code]mappend[/code] between [code]mempty[/code] and some other monoid value, the result is that other monoid value. So if we use [code]return[/code] to make a [code]Writer[/code] value and then use [code]&gt;&gt;=[/code] to feed that value to a function, the resulting monoid value will be only what the function returns. Let's use [code]return[/code] on the number [code]3[/code] a bunch of times, only we'll pair it with a different monoid every time:

Because [code]Writer[/code] doesn't have a [code]Show[/code] instance, we had to use [code]runWriter[/code] to convert our [code]Writer[/code] values to normal tuples that can be shown. For [code]String[/code], the monoid value is the empty string. With [code]Sum[/code], it's [code]0[/code], because if we add 0 to something, that something stays the same. For [code]Product[/code], the identity is [code]1[/code].

The [code]Writer[/code] instance doesn't feature an implementation for [code]fail[/code], so if a pattern match fails in [code]do[/code] notation, [code]error[/code] is called.


Using do notation with Writer

Now that we have a [code]Monad[/code] instance, we're free to use [code]do[/code] notation for [code]Writer[/code] values. It's handy for when we have a several [code]Writer[/code] values and we want to do stuff with them. Like with other monads, we can treat them as normal values and the context gets taken for us. In this case, all the monoid values that come attached get [code]mappend[/code]ed and so are reflected in the final result. Here's a simple example of using [code]do[/code] notation with [code]Writer[/code] to multiply two numbers:

[code]logNumber[/code] takes a number and makes a [code]Writer[/code] value out of it. For the monoid, we use a list of strings and we equip the number with a singleton list that just says that we have that number. [code]multWithLog[/code] is a [code]Writer[/code] value which multiplies [code]3[/code] and [code]5[/code] and makes sure that their attached logs get included in the final log. We use [code]return[/code] to present [code]a*b[/code] as the result. Because [code]return[/code] just takes something and puts it in a minimal context, we can be sure that it won't add anything to the log. Here's what we see if we run this:

Sometimes we just want some monoid value to be included at some particular point. For this, the [code]tell[/code] function is useful. It's part of the [code]MonadWriter[/code] type class and in the case of [code]Writer[/code] it takes a monoid value, like [code]["This is going on"][/code] and creates a [code]Writer[/code] value that presents the dummy value [code]()[/code] as its result but has our desired monoid value attached. When we have a monadic value that has [code]()[/code] as its result, we don't bind it to a variable. Here's [code]multWithLog[/code] but with some extra reporting included:

It's important that [code]return (a*b)[/code] is the last line, because the result of the last line in a [code]do[/code] expression is the result of the whole [code]do[/code] expression. Had we put [code]tell[/code] as the last line, [code]()[/code] would have been the result of this [code]do[/code] expression. We'd lose the result of the multiplication. However, the log would be the same. Here is this in action:


Adding logging to programs

Euclid's algorithm is an algorithm that takes two numbers and computes their greatest common divisor. That is, the biggest number that still divides both of them. Haskell already features the [code]gcd[/code] function, which does exactly this, but let's implement our own and then equip it with logging capabilities. Here's the normal algorithm:

The algorithm is very simple. First, it checks if the second number is 0. If it is, then the result is the first number. If it isn't, then the result is the greatest common divisor of the second number and the remainder of dividing the first number with the second one. For instance, if we want to know what the 
greatest common divisor of 8 and 3 is, we just follow the algorithm outlined. Because 3 isn't 0, we have to find the greatest common divisor of 3 and 2 (if we divide 8 by 3, the remainder is 2). Next, we find the greatest common divisor of 3 and 2. 2 still isn't 0, so now we have have 2 and 1. The second number isn't 0, so we run the algorithm again for 1 and 0, as dividing 2 by 1 gives us a remainder of 0. And finally, because the second number is now 0, the final result is 1. Let's see if our code agrees:

It does. Very good! Now, we want to equip our result with a context, and the context will be a monoid value that acts as a log. Like before, we'll use a list of strings as our monoid. So the type of our new [code]gcd'[/code] function should be:

All that's left now is to equip our function with log values. Here's the code:

This function takes two normal [code]Int[/code] values and returns a [code]Writer [String] Int[/code], that is, an [code]Int[/code] that has a log context. In the case where [code]b[/code] is [code]0[/code], instead of just giving [code]a[/code] as the result, we use a [code]do[/code] expression to put together a [code]Writer[/code] value as a result. First we use [code]tell[/code] to report that we're finished and then we use [code]return[/code] to present [code]a[/code] as the result of the [code]do[/code] expression. Instead of this [code]do[/code] expression, we could have also written this:

However, I think the [code]do[/code] expression is easier to read. Next, we have the case when [code]b[/code] isn't [code]0[/code]. In this case, we log that we're using [code]mod[/code] to figure out the remainder of dividing [code]a[/code] and [code]b[/code]. Then, the second line of the [code]do[/code] expression just recursively calls [code]gcd'[/code]. Remember, [code]gcd'[/code] now ultimately returns a [code]Writer[/code] value, so it's perfectly valid that [code]gcd' b (a `mod` b)[/code] is a line in a [code]do[/code] expression.

While it may be kind of useful to trace the execution of this new [code]gcd'[/code] by hand to see how the logs get appended, I think it's more insightful to just look at the big picture and view these as values with a context and from that gain insight as to what the final result will be.

Let's try our new [code]gcd'[/code] out. Its result is a [code]Writer [String] Int[/code] value and if we unwrap that from its [code]newtype[/code], we get a tuple. The first part of the tuple is the result. Let's see if it's okay:

Good! Now what about the log? Because the log is a list of strings, let's use [code]mapM_ putStrLn[/code] to print those strings to the screen:

I think it's awesome how we were able to change our ordinary algorithm to one that reports what it does as it goes along just by changing normal values to monadic values and letting the implementation of [code]&gt;&gt;=[/code] for [code]Writer[/code] take care of the logs for us. We can add a logging mechanism to pretty much any function. We just replace normal values with [code]Writer[/code] values where we want and change normal function application to [code]&gt;&gt;=[/code] (or [code]do[/code] expressions if it increases readability).


Inefficient list construction

When using the [code]Writer[/code] monad, you have to be careful which monoid to use, because using lists can sometimes turn out to be very slow. That's because lists use [code]++[/code] for [code]mappend[/code] and using [code]++[/code] to add something to the end of a list is slow if that list is really long.

In our [code]gcd'[/code] function, the logging is fast because the list appending ends up looking like this:

Lists are a data structure that's constructed from left to right, and this is efficient because we first fully construct the left part of a list and only then add a longer list on the right. But if we're not careful, using the [code]Writer[/code] monad can produce list appending that looks like this:

This associates to the left instead of to the right. This is inefficient because every time it wants to add the right part to the left part, it has to construct the left part all the way from the beginning!

The following function works like [code]gcd'[/code], only it logs stuff in reverse. First it produces the log for the rest of the procedure and then adds the current step to the end of the log. 

It does the recursion first, and binds its result value to [code]result[/code]. Then it adds the current step to the log, but the current step goes at the end of the log that was produced by the recursion. Finally, it presents the result of the recursion as the final result. Here it is in action:

It's inefficient because it ends up associating the use of [code]++[/code] to the left instead of to the right.


Difference lists

Because lists can sometimes be inefficient when repeatedly appended in this manner, it's best to use a data structure that always supports efficient appending. One such data structure is the difference list.  A difference list is similar to a list, only instead of being a normal list, it's a function that takes a list and prepends another list to it. The difference list equivalent of a list like [code][1,2,3][/code] would be the function [code]\xs -&gt; [1,2,3] ++ xs[/code]. A normal empty list is [code][][/code], whereas an empty difference list is the function [code]\xs -&gt; [] ++ xs[/code].

The cool thing about difference lists is that they support efficient appending. When we append two normal lists with [code]++[/code], it has to walk all the way to the end of the list on the left of [code]++[/code] and then stick the other one there. But what if we take the difference list approach and represent our lists as functions? Well then, appending two difference lists can be done like so:

Remember, [code]f[/code] and [code]g[/code] are functions that take lists and prepend something to them. So, for instance, if [code]f[/code] is the function [code]("dog"++)[/code] (just another way of writing [code]\xs -&gt; "dog" ++ xs[/code]) and [code]g[/code] the function [code]("meat"++)[/code], then [code]f `append` g[/code] makes a new function that's equivalent to the following:

We've appended two difference lists just by making a new function that first applies one difference list some list and then the other.

Let's make a [code]newtype[/code] wrapper for difference lists so that we can easily give them monoid instances:

The type that we wrap is [code][a] -&gt; [a][/code] because a difference list is just a function that takes a list and returns another. Converting normal lists to difference lists and vice versa is easy:

To make a normal list into a difference list we just do what we did before and make it a function that prepends it to another list. Because a difference list is a function that prepends something to another list, if we just want that something, we apply the function to an empty list!

Here's the [code]Monoid[/code] instance:

Notice how for lists, [code]mempty[/code] is just the [code]id[/code] function and [code]mappend[/code] is actually just function composition. Let's see if this works:

Tip top! Now we can increase the efficiency of our [code]gcdReverse[/code] function by making it use difference lists instead of normal lists:

We only had to change the type of the monoid from [code][String][/code] to [code]DiffList String[/code] and then when using [code]tell[/code], convert our normal lists into difference lists with [code]toDiffList[/code]. Let's see if the log gets assembled properly:

We do [code]gcdReverse 110 34[/code], then use [code]runWriter[/code] to unwrap it from the [code]newtype[/code], then apply [code]snd[/code] to that to just get the log, then apply [code]fromDiffList[/code] to convert it to a normal list and then finally print its entries to the screen.


Comparing Performance

To get a feel for just how much difference lists may improve your performance, consider this function that just counts down from some number to zero, but produces its log in reverse, like [code]gcdReverse[/code], so that the numbers in the log will actually be counted up:

If we give it [code]0[/code], it just logs it. For any other number, it first counts down its predecessor to [code]0[/code] and then appends that number to the log. So if we apply [code]finalCountDown[/code] to [code]100[/code], the string [code]"100"[/code] will come last in the log.

Anyway, if you load this function in GHCi and apply it to a big number, like [code]500000[/code], you'll see that it quickly starts counting from [code]0[/code] onwards:

However, if we change it to use normal lists instead of difference lists, like so:

And then tell GHCi to start counting:

We'll see that the counting is really slow.

Of course, this is not the proper and scientific way to test how fast our programs are, but we were able to see that in this case, using difference lists starts producing results right away whereas normal lists take forever.

Oh, by the way, the song Final Countdown by Europe is now stuck in your head.
Enjoy!