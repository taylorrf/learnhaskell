do notation
===========

Monads in Haskell are so useful that they got their own special syntax called [code]do[/code] notation. We've already encountered [code]do[/code] notation when we were doing I/O and there we said that it was for gluing together several I/O actions into one. Well, as it turns out, [code]do[/code] notation isn't just for [code]IO[/code], but can be used for any monad. Its principle is still the same: gluing together monadic values in sequence. We're going to take a look at how [code]do[/code] notation works and why it's useful.

Consider this familiar example of monadic application:

Been there, done that. Feeding a monadic value to a function that returns one, no big deal. Notice how when we do this, [code]x[/code] becomes [code]3[/code] inside the lambda. Once we're inside that lambda, it's just a normal value rather than a monadic value. Now, what if we had another [code]&gt;&gt;=[/code] inside that function? Check this out:

Ah, a nested use of [code]&gt;&gt;=[/code]! In the outermost lambda, we feed [code]Just "!"[/code] to the lambda [code]\y -&gt; Just (show x ++ y)[/code]. Inside this lambda, the [code]y[/code] becomes [code]"!"[/code]. [code]x[/code] is still [code]3[/code] because we got it from the outer lambda. All this sort of reminds me of the following expression:

The main difference between these two is that the values in the former example are monadic. They're values with a failure context. We can replace any of them with a failure:

In the first line, feeding a [code]Nothing[/code] to a function naturally results in a [code]Nothing[/code]. In the second line, we feed [code]Just 3[/code] to a function and the [code]x[/code] becomes [code]3[/code], but then we feed a [code]Nothing[/code] to the inner lambda and the result of that is [code]Nothing[/code], which causes the outer lambda to produce [code]Nothing[/code] as well. So this is sort of like assigning values to variables in [code]let[/code] expressions, only that the values in question are monadic values.

To further illustrate this point, let's write this in a script and have each [code]Maybe[/code] value take up its own line:

To save us from writing all these annoying lambdas, Haskell gives us [code]do[/code] notation. It allows us to write the previous piece of code like this:

It would seem as though we've gained the ability to temporarily extract things from [code]Maybe[/code] values without having to check if the [code]Maybe[/code] values are [code]Just[/code] values or [code]Nothing[/code] values at every step. How cool! If any of the values that we try to extract from are [code]Nothing[/code], the whole [code]do[/code] expression will result in a [code]Nothing[/code]. We're yanking out their (possibly existing) values and letting [code]&gt;&gt;=[/code] worry about the context that comes with those values. It's important to remember that [code]do[/code] expressions are just different syntax for chaining monadic values.

In a [code]do[/code] expression, every line is a monadic value. To inspect its result, we use [code]&lt;-[/code]. If we have a [code]Maybe String[/code] and we bind it with [code]&lt;-[/code] to a variable, that variable will be a [code]String[/code], just like when we used [code]&gt;&gt;=[/code] to feed monadic values to lambdas. The last monadic value in a  [code]do[/code] expression, like [code]Just (show x ++ y)[/code] here, can't be used with [code]&lt;-[/code] to bind its result, because that wouldn't make sense if we translated the [code]do[/code] expression back to a chain of [code]&gt;&gt;=[/code] applications. Rather, its result is the result of the whole glued up monadic value, taking into account the possible failure of any of the previous ones.

For instance, examine the following line:

Because the left parameter of [code]&gt;&gt;=[/code] is a [code]Just[/code] value, the lambda is applied to [code]9[/code] and the result is a [code]Just True[/code]. If we rewrite this in [code]do[/code] notation, we get:

If we compare these two, it's easy to see why the result of the whole monadic value is the result of the last monadic value in the [code]do[/code] expression with all the previous ones chained into it.

Our tightwalker's routine can also be expressed with [code]do[/code] notation. [code]landLeft[/code] and [code]landRight[/code] take a number of birds and a pole and produce a pole wrapped in a [code]Just[/code], unless the tightwalker slips, in which case a [code]Nothing[/code] is produced. We used [code]&gt;&gt;=[/code] to chain successive steps because each one relied on the previous one and each one had an added context of possible failure. Here's two birds landing on the left side, then two birds landing on the right and then one bird landing on the left:

Let's see if he succeeds:

He does! Great. When we were doing these routines by explicitly writing [code]&gt;&gt;=[/code], we usually said something like [code]return (0,0) &gt;&gt;= landLeft 2[/code], because [code]landLeft 2[/code] is a function that returns a [code]Maybe[/code] value. With [code]do[/code] expressions however, each line must feature a monadic value. So we explicitly pass the previous [code]Pole[/code] to the [code]landLeft[/code] [code]landRight[/code] functions. If we examined the variables to which we bound our [code]Maybe[/code] values, [code]start[/code] would be [code](0,0)[/code], [code]first[/code] would be [code](2,0)[/code] and so on.

Because [code]do[/code] expressions are written line by line, they may look like imperative code to some people. But the thing is, they're just sequential, as each value in each line relies on the result of the previous ones, along with their contexts (in this case, whether they succeeded or failed).

Again, let's take a look at what this piece of code would look like if we hadn't used the monadic aspects of [code]Maybe[/code]:

See how in the case of success, the tuple inside [code]Just (0,0)[/code] becomes [code]start[/code], the result of [code]landLeft 2 start[/code] becomes [code]first[/code], etc.

If we want to throw the Pierre a banana peel in [code]do[/code] notation, we can do the following:

When we write a line in [code]do[/code] notation without binding the monadic value with [code]&lt;-[/code], it's just like putting [code]&gt;&gt;[/code] after the monadic value whose result we want to ignore. We sequence the monadic value but we ignore its result because we don't care what it is and it's prettier than writing [code]_ &lt;- Nothing[/code], which is equivalent to the above.

When to use [code]do[/code] notation and when to explicitly use [code]&gt;&gt;=[/code] is up to you. I think this example lends itself to explicitly writing [code]&gt;&gt;=[/code] because each step relies specifically on the result of the previous one. With [code]do[/code] notation, we had to specifically write on which pole the birds are landing, but every time we used that came directly before. But still, it gave us some insight into [code]do[/code] notation.

In [code]do[/code] notation, when we bind monadic values to names, we can utilize pattern matching, just like in [code]let[/code] expressions and function parameters. Here's an example of pattern matching in a [code]do[/code] expression:

We use pattern matching to get the first character of the string [code]"hello"[/code] and then we present it as the result. So [code]justH[/code] evaluates to [code]Just 'h'[/code].

What if this pattern matching were to fail? When matching on a pattern in a function fails, the next pattern is matched. If the matching falls through all the patterns for a given function, an error is thrown and our program crashes. On the other hand, failed pattern matching in [code]let[/code] expressions results in an error being produced right away, because the mechanism of falling through patterns isn't present in [code]let[/code] expressions. When pattern matching fails in a [code]do[/code] expression, the [code]fail[/code] function is called. It's part of the [code]Monad[/code] type class and it enables failed pattern matching to result in a failure in the context of the current monad instead of making our program crash. Its default implementation is this:

So by default it does make our program crash, but monads that incorporate a context of possible failure (like [code]Maybe[/code]) usually implement it on their own. For [code]Maybe[/code], its implemented like so:

It ignores the error message and makes a [code]Nothing[/code]. So when pattern matching fails in a [code]Maybe[/code] value that's written in [code]do[/code] notation, the whole value results in a [code]Nothing[/code]. This is preferable to having our program crash. Here's a [code]do[/code] expression with a pattern that's bound to fail:

The pattern matching fails, so the effect is the same as if the whole line with the pattern was replaced with a [code]Nothing[/code]. Let's try this out:

The failed pattern matching has caused a failure within the context of our monad instead of causing a program-wide failure, which is pretty neat.