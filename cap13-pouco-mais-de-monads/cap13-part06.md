Some useful monadic functions
=============================

In this section, we're going to explore a few functions that either operate on monadic values or return monadic values as their results (or both!). Such functions are usually referred to as monadic functions. While some of them will be brand new, others will be monadic counterparts of functions that we already know, like [code]filter[/code] and [code]foldl[/code]. Let's see what they are then!

- liftM and friends

When we started our journey to the top of Monad Mountain, we first looked at functors, which are for things that can be mapped over. Then, we learned about improved functors called applicative functors, which allowed us to apply normal functions between several applicative values as well as to take a normal value and put it in some default context. Finally, we introduced monads as improved applicative functors, which added the ability for these values with context to somehow be fed into normal functions.

So every monad is an applicative functor and every applicative functor is a functor. The [code]Applicative[/code] type class has a class constraint such that our type has to be an instance of [code]Functor[/code] before we can make it an instance of [code]Applicative[/code]. But even though [code]Monad[/code] hould have the same constraint for [code]Applicative[/code], as every monad is an applicative functor, it doesn't, because the [code]Monad[/code] type class was introduced to Haskell way before [code]Applicative[/code].

But even though every monad is a functor, we don't have to rely on it having a [code]Functor[/code] instance because of the [code]liftM[/code] function. [code]liftM[/code] takes a function and a monadic value and maps it over the monadic value. So it's pretty much the same thing as [code]fmap[/code]! This is [code]liftM[/code]'s type:

And this is the type of [code]fmap[/code]:

If the [code]Functor[/code] and [code]Monad[/code] instances for a type obey the functor and monad laws, these two amount to the same thing (and all the monads that we've met so far obey both). This is kind of like [code]pure[/code] and [code]return[/code] do the same thing, only one has an [code]Applicative[/code] class constraint whereas the other has a [code]Monad[/code] one. Let's try [code]liftM[/code] out:

We already know quite well how [code]fmap[/code] works with [code]Maybe[/code] values. And [code]liftM[/code] does the same thing. For [code]Writer[/code] values, the function is mapped over the first component of the tuple, which is the result. Doing [code]fmap[/code] or [code]liftM[/code] over a stateful computation results in another stateful computation, only its eventual result is modified by the supplied function. Had we not mapped [code](+100)[/code] over [code]pop[/code] in this case before running it, it would have returned [code](1,[2,3,4])[/code].


This is how [code]liftM[/code] is implemented:

Or with [code]do[/code] notation:

We feed the monadic value [code]m[/code] into the function and then we apply the function [code]f[/code] to its result before putting it back into a default context. Because of the monad laws, this is guaranteed not to change the context, only the result that the monadic value presents. We see that [code]liftM[/code] is implemented without referencing the [code]Functor[/code] type class at all. This means that we can implement [code]fmap[/code] (or [code]liftM[/code], whatever you want to call it) just by using the goodies that monads offer us. Because of this, we can conclude that monads are stronger than just regular old functors.

The [code]Applicative[/code] type class allows us to apply functions between values with contexts as if they were normal values. Like this:

Using this applicative style makes things pretty easy. [code]&lt;$&gt;[/code] is just [code]fmap[/code] and [code]&lt;*&gt;[/code] is a function from the [code]Applicative[/code] type class that has the following type:

So it's kind of like [code]fmap[/code], only the function itself is in a context. We have to somehow extract it from the context and map it over the [code]f a[/code] value and then assemble the context back together. Because all functions are curried in Haskell by default, we can use the combination of [code]&lt;$&gt;[/code] and [code]&lt;*&gt;[/code] to apply functions that take several parameters between applicative values.

Anyway, it turns out that just like [code]fmap[/code], [code]&lt;*&gt;[/code] can also be implemented by using only what the [code]Monad[/code] type class give us. The [code]ap[/code] function is basically [code]&lt;*&gt;[/code], only it has a [code]Monad[/code] constraint instead of an [code]Applicative[/code] one. Here's its definition:

[code]mf[/code] is a monadic value whose result is a function. Because the function is in a context as well as the value, we get the function from the context and call it [code]f[/code], then get the value and call that [code]x[/code] and then finally apply the function to the value and present that as a result. Here's a quick demonstration:

Now we see that monads are stronger than applicatives as well, because we can use the functions from [code]Monad[/code] to implement the ones for [code]Applicative[/code]. In fact, many times when a type is found to be a monad, people first write up a [code]Monad[/code] instance and then make an [code]Applicative[/code] instance by just saying that [code]pure[/code] is [code]return[/code] and [code]&lt;*&gt;[/code] is [code]ap[/code]. Similarly, if you already have a [code]Monad[/code] instance for something, you can give it a [code]Functor[/code] instance just saying that [code]fmap[/code] is [code]liftM[/code].

The [code]liftA2[/code] function is a convenience function for applying a function between two applicative values. It's defined simply like so:

The [code]liftM2[/code] function does the same thing, only it has a [code]Monad[/code] constraint. There also exist [code]liftM3[/code] and [code]liftM4[/code] and [code]liftM5[/code].

We saw how monads are stronger than applicatives and functors and how even though all monads are functors and applicative functors, they don't necessarily have [code]Functor[/code] and [code]Applicative[/code] instances, so we examined the monadic equivalents of the functions that functors and applicative functors use.


- The join function


Here's some food for thought: if the result of one monadic value is another monadic value i.e. if one monadic value is nested inside the other, can you flatten them to just a single normal monadic value? Like, if we have [code]Just (Just 9)[/code], can we make that into [code]Just 9[/code]? It turns out that any nested monadic value can be flattened and that this is actually a property unique to monads. For this, the [code]join[/code] function exists. Its type is this:

So it takes a monadic value within a monadic value and gives us just a monadic value, so it sort of flattens it. Here it is with some [code]Maybe[/code] values:

The first line has a successful computation as a result of a successful computation, so they're both just joined into one big successful computation. The second line features a [code]Nothing[/code] as a result of a [code]Just[/code] value. Whenever we were dealing with [code]Maybe[/code] values before and we wanted to combine several of them into one, be it with [code]&lt;*&gt;[/code] or [code]&gt;&gt;=[/code], they all had to be [code]Just[/code] values for the result to be a [code]Just[/code] value. If there was any failure along the way, the result was a failure and the same thing happens here. In the third line, we try to flatten what is from the onset a failure, so the result is a failure as well.

Flattening lists is pretty intuitive:

As you can see, for lists, [code]join[/code] is just [code]concat[/code]. To flatten a [code]Writer[/code] value whose result is a [code]Writer[/code] value itself, we have to [code]mappend[/code] the monoid value.
 

The outer monoid value [code]"bbb"[/code] comes first and then to it [code]"aaa"[/code] is appended. Intuitively speaking, when you want to examine what the result of a [code]Writer[/code] value is, you have to write its monoid value to the log first and only then can you examine what it has inside.

Flattening [code]Either[/code] values is very similar to flattening [code]Maybe[/code] values:

If we apply [code]join[/code] to a stateful computation whose result is a stateful computation, the result is a stateful computation that first runs the outer stateful computation and then the resulting one. Watch:

The lambda here takes a state and puts [code]2[/code] and [code]1[/code] onto the stack and presents [code]push 10[/code] as its result. So when this whole thing is flattened with [code]join[/code] and then run, it first puts [code]2[/code] and [code]1[/code] onto the stack and then [code]push 10[/code] gets carried out, pushing a [code]10[/code] on to the top.


The implementation for [code]join[/code] is as follows:

Because the result of [code]mm[/code] is a monadic value, we get that result and then just put it on a line of its own because it's a monadic value. The trick here is that when we do [code]m &lt;- mm[/code], the context of the monad in which we are in gets taken care of. That's why, for instance, [code]Maybe[/code] values result in [code]Just[/code] values only if the outer and inner values are both [code]Just[/code] values. Here's what this would look like if the [code]mm[/code] value was set in advance to [code]Just (Just 8)[/code]:

Perhaps the most interesting thing about [code]join[/code] is that for every monad, feeding a monadic value to a function with [code]&gt;&gt;=[/code] is the same thing as just mapping that function over the value and then using [code]join[/code] to flatten the resulting nested monadic value! In other words, [code]m &gt;&gt;= f[/code] is always the same thing as [code]join (fmap f m)[/code]! It makes sense when you think about it. With [code]&gt;&gt;=[/code], we're always thinking about how to feed a monadic value to a function that takes a normal value but returns a monadic value. If we just map that function over the monadic value, we have a monadic value inside a monadic value. For instance, say we have [code]Just 9[/code] and the function [code]\x -&gt; Just (x+1)[/code]. If we map this function over [code]Just 9[/code], we're left with [code]Just (Just 10)[/code].

The fact that [code]m &gt;&gt;= f[/code] always equals [code]join (fmap f m)[/code] is very useful if we're making our own [code]Monad[/code] instance for some type because it's often easier to figure out how we would flatten a nested monadic value than figuring out how to implement [code]&gt;&gt;=[/code].


- filterM

The [code]filter[/code] function is pretty much the bread of 
Haskell programming ([code]map[/code] being the butter). It takes 
a predicate and a list to filter out and then returns a new list where only the 
elements that satisfy the predicate are kept. Its type is this:


The predicate takes an element of the list and returns a [code]Bool[/code] value. Now, what if the [code]Bool[/code] value that it returned
was actually a monadic value? Whoa! That is, what if it came with a context? 
Could that work? For instance, what if every [code]True[/code] or a [code]False[/code] value that the 
predicate produced also had an accompanying monoid value, like [code]["Accepted the number 5"][/code] or [code]["3 is too 
small"][/code]? That sounds like it could work. If that were the case, we'd 
expect the resulting list to also come with a log of all the log values that 
were produced along the way. So if the [code]Bool[/code] that the 
predicate returned came with a context, we'd expect the final resulting list to have 
some context attached as well, otherwise the context that each [code]Bool[/code] came with would be lost.


The [code]filterM[/code] function from [code]Control.Monad[/code] 
does just what we want! Its type is this:


The predicate returns a monadic value whose result is a [code]Bool[/code], but because it's a monadic value, its context can be 
anything from a possible failure to non-determinism and more! To ensure that the 
context is reflected in the final result, the result is also a monadic value.

Let's take a list and only keep those values that are smaller than 4. To 
start, we'll just use the regular [code]filter[/code] function:


That's pretty easy. Now, let's make a predicate that, aside from presenting a 
[code]True[/code] or [code]False[/code] result, also 
provides a log of what it did. Of course, we'll be using the [code]Writer[/code] monad for this:

Instead of just and returning a [code]Bool[/code], this function 
returns a [code]Writer [String] Bool[/code]. It's a monadic 
predicate. Sounds fancy, doesn't it? If the number is smaller than [code]4[/code] we report that we're keeping it and then [code]return True[/code].


Now, let's give it to [code]filterM[/code] along with a list. 
Because the predicate returns a 

[code]Writer[/code] value, the resulting list will also 
be a [code]Writer[/code] value.


Examining the result of the resulting [code]Writer[/code] value, 
we see that everything is in order. Now, let's print the log and see what we 
got:

Awesome. So just by providing a monadic predicate to [code]filterM[/code], 
we were able to filter a list while taking advantage of the monadic context that 
we used.


A very cool Haskell trick is using [code]filterM[/code] to get the 
powerset of a list (if we think of them as sets for now). The powerset of some 
set is a set of all subsets of that set. So if we have a set like [code][1,2,3][/code], its powerset would include the following sets:

In other words, getting a powerset is like getting all the combinations of 
keeping and throwing out elements from a set. [code][2,3][/code] 
is like the original set, only we excluded the number [code]1[/code].


To make a function that returns a powerset of some list, we're going to rely on 
non-determinism. We take the list [code][1,2,3][/code] and then 
look at the first element, which is [code]1[/code] and we ask 
ourselves: should we keep it or drop it? Well, we'd like to do both actually. So 
we are going to filter a list and we'll use a predicate that non-deterministically 
both keeps and drops every element from the list. Here's our [code]powerset[/code] function:


Wait, that's it? Yup. We choose to drop and keep every element, regardless of 
what that element is. We have a non-deterministic predicate, so the resulting 
list will also be a non-deterministic value and will thus be a list of lists. Let's 
give this a go:


This takes a bit of thinking to wrap your head around, but if you just consider 
lists as non-deterministic values that don't know what to be so they just decide 
to be everything at once, it's a bit easier.


- foldM

The monadic counterpart to [code]foldl[/code] is [code]foldM[/code]. If you remember your folds from the <a href="folds">folds section</a>, you know that [code]foldl[/code] takes a binary function, a starting accumulator and a list to fold up and then folds it from the left into a single value by using the binary function. [code]foldM[/code] does the same thing, except it takes a binary function that produces a monadic value and folds the list up with that. Unsurprisingly, the resulting value is also monadic. The type of [code]foldl[/code] is this:

Whereas [code]foldM[/code] has the following type:

The value that the binary function returns is monadic and so the result of the whole fold is monadic as well. Let's sum a list of numbers with a fold:

The starting accumulator is [code]0[/code] and then [code]2[/code] gets added to the accumulator, resulting in a new accumulator that has a value of [code]2[/code]. [code]8[/code] gets added to this accumulator resulting in an accumulator of [code]10[/code] and so on and when we reach the end, the final accumulator is the result.

Now what if we wanted to sum a list of numbers but with the added condition that if any number is greater than [code]9[/code] in the list, the whole thing fails? It would make sense to use a binary function that checks if the current number is greater than [code]9[/code] and if it is, fails, and if it isn't, continues on its merry way. Because of this added possibility of failure, let's make our binary function return a [code]Maybe[/code] accumulator instead of a normal one. Here's the binary function:

Because our binary function is now a monadic function, we can't use it with the normal [code]foldl[/code], but we have to use [code]foldM[/code]. Here goes:

Excellent! Because one number in the list was greater than [code]9[/code], the whole thing resulted in a [code]Nothing[/code]. Folding with a binary function that returns a [code]Writer[/code] value is cool as well because then you log whatever you want as your fold goes along its way.

- Making a safe RPN calculator

When we were solving the problem of <a href="reverse-polish-notation-calculator">implementing a RPN calculator</a>, we noted that it worked fine as long as the input that it got made sense. But if something went wrong, it caused our whole program to crash. Now that we know how to take some code that we have and make it monadic, let's take our RPN calculator and add error handling to it by taking advantage of the [code]Maybe[/code] monad.

We implemented our RPN calculator by taking a string like [code]"1 3 + 2 *"[/code], breaking it up into words to get something like [code]["1","3","+","2","*"][/code] and then folding over that list by starting out with an empty stack and then using a binary folding function that adds numbers to the stack or manipulates numbers on the top of the stack to add them together and divide them and such.

This was the main body of our function:

We made the expression into a list of strings, folded over it with our folding function and then when we were left with just one item in the stack, we returned that item as the answer. This was the folding function:

The accumulator of the fold was a stack, which we represented with a list of [code]Double[/code] values. As the folding function went over the RPN expression, if the current item was an operator, it took two items off the top of the stack, applied the operator between them and then put the result back on the stack. If the current item was a string that represented a number, it converted that string into an actual number and returned a new stack that was like the old one, except with that number pushed to the top.

Let's first make our folding function capable of graceful failure. Its type is going to change from what it is now to this:

So it will either return [code]Just[/code] a new stack or it will fail with [code]Nothing[/code]. 

The [code]reads[/code] function is like [code]read[/code], only it returns a list with a single element in case of a successful read. If it fails to read something, then it returns an empty list. Apart from returning the value that it read, it also returns the part of the string that it didn't consume. We're going to say that it always has to consume the full input to work and make it into a [code]readMaybe[/code] function for convenience. Here it is:

Testing it out:

Okay, it seems to work. So, let's make our folding function into a monadic function that can fail:


The first three cases are like the old ones, except the new stack gets wrapped in a [code]Just[/code] (we used [code]return[/code] here to do this, but we could have written [code]Just[/code] just as well). In the last case, we do [code]readMaybe numberString[/code] and then we map [code](:xs)[/code] over it. So if the stack [code]xs[/code] is [code][1.0,2.0][/code] and [code]readMaybe numberString[/code] results in a [code]Just 3.0[/code], the result is [code]Just [3.0,1.0,2.0][/code]. If [code]readMaybe numberString[/code] results in a [code]Nothing[/code] then the result is [code]Nothing[/code]. Let's try out the folding function by itself:

It looks like it's working! And now it's time for the new and improved [code]solveRPN[/code]. Here it is ladies and gents!

Just like before, we take the string and make it into a list of words. Then, we do a fold, starting with the empty stack, only instead of doing a normal [code]foldl[/code], we do a [code]foldM[/code]. The result of that [code]foldM[/code] should be a [code]Maybe[/code] value that contains a list (that's our final stack) and that list should have only one value. We use a [code]do[/code] expression to get that value and we call it [code]result[/code]. In case the [code]foldM[/code] returns a [code]Nothing[/code], the whole thing will be a [code]Nothing[/code], because that's how [code]Maybe[/code] works. Also notice that we pattern match in the [code]do[/code] expression, so if the list has more than one value or none at all, the pattern match fails and a [code]Nothing[/code] is produced. In the last line we just do [code]return result[/code] to present the result of the RPN calculation as the result of the final [code]Maybe[/code] value.


Let's give it a shot:

The first failure happens because the final stack isn't a list with one element in it and so the pattern matching in the [code]do[/code] expression fails. The second failure happens because [code]readMaybe[/code] returns a [code]Nothing[/code].

- Composing monadic functions

When we were learning about the monad laws, we said that the [code]&lt;=&lt;[/code] function is just like composition, only instead of working for normal functions like [code]a -&gt; b[/code], it works for monadic functions like [code]a -&gt; m b[/code]. For instance:

In this example we first composed two normal functions, applied the resulting function to [code]4[/code] and then we composed two monadic functions and fed [code]Just 4[/code] to the resulting function with [code]&gt;&gt;=[/code].

If we have a bunch of functions in a list, we can compose them one all into one big function by just using [code]id[/code] as the starting accumulator and the [code].[/code] function as the binary function. Here's an example:

The function [code]f[/code] takes a number and then adds [code]1[/code] to it, multiplies the result by [code]100[/code] and then adds [code]1[/code] to that. Anyway, we can compose monadic functions in the same way, only instead normal composition we use [code]&lt;=&lt;[/code] and instead of [code]id[/code] we use [code]return[/code]. We don't have to use a [code]foldM[/code] over a [code]foldr[/code] or anything because the [code]&lt;=&lt;[/code] function makes sure that composition happens in a monadic fashion.

When we were getting to know the list monad in the <a href="a-fistful-of-monads#the-list-monad">previous chapter</a>, we used it to figure out if a knight can go from one position on a chessboard to another in exactly three moves. We had a function called [code]moveKnight[/code] which took the knight's position on the board and returned all the possible moves that he can make next. Then, to generate all the possible positions that he can have after taking three moves, we made the following function:

And to check if he can go from [code]start[/code] to [code]end[/code] in three moves, we did the following:

Using monadic function composition, we can make a function like [code]in3[/code], only instead of generating all the positions that the knight can have after making three moves, we can do it for an arbitrary number of moves. If you look at [code]in3[/code], we see that we used [code]moveKnight[/code] three times and each time we used [code]&gt;&gt;=[/code] to feed it all the possible previous positions. So now, let's make it more general. Here's how to do it:

First we use [code]replicate[/code] to make a list that contains [code]x[/code] copies of the function [code]moveKnight[/code]. Then, we monadically compose all those functions into one, which gives us a function that takes a starting position and non-deterministically moves the knight [code]x[/code] times. Then, we just make the starting position into a singleton list with [code]return[/code] and feed it to the function.

Now, we can change our [code]canReachIn3[/code] function to be more general as well: