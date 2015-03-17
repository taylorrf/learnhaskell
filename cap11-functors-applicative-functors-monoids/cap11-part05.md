Monoids
=======

Type classes in Haskell are used to present an interface for types that have some behavior in common. We started out with simple type classes like [code]Eq[/code], which is for types whose values can be equated, and [code]Ord[/code], which is for things that can be put in an order and then moved on to more interesting ones, like [code]Functor[/code] and [code]Applicative[/code].

When we make a type, we think about which behaviors it supports, i.e. what it can act like and then based on that we decide which type classes to make it an instance of. If it makes sense for values of our type to be equated, we make it an instance of the [code]Eq[/code] type class. If we see that our type is some kind of functor, we make it an instance of [code]Functor[/code], and so on.

Now consider the following: [code]*[/code] is a function that takes two numbers and multiplies them. If we multiply some number with a [code]1[/code], the result is always equal to that number. It doesn't matter if we do [code]1 * x[/code] or [code]x * 1[/code], the result is always [code]x[/code]. Similarly, [code]++[/code] is also a function which takes two things and returns a third. Only instead of multiplying numbers, it takes two lists and concatenates them. And much like [code]*[/code], it also has a certain value which doesn't change the other one when used with [code]++[/code]. That value is the empty list: [code][][/code].

It seems that both [code]*[/code] together with [code]1[/code] and [code]++[/code] along with [code][][/code] share some common properties:

- The function takes two parameters.
- The parameters and the returned value have the same type.
- There exists such a value that doesn't change other values when used with the binary function.

There's another thing that these two operations have in common that may not be as obvious as our previous observations: when we have three or more values and we want to use the binary function to reduce them to a single result, the order in which we apply the binary function to the values doesn't matter. It doesn't matter if we do [code](3 * 4) * 5[/code] or [code]3 * (4 * 5)[/code]. Either way, the result is [code]60[/code]. The same goes for [code]++[/code]:

We call this property <i>associativity</i>. [code]*[/code] is associative, and so is [code]++[/code], but [code]-[/code], for example, is not. The expressions [code](5 - 3) - 4[/code] and [code]5 - (3 - 4)[/code] result in different numbers.

By noticing and writing down these properties, we have chanced upon <i>monoids</i>! A monoid is when you have an associative binary function and a value which acts as an identity with respect to that function. When something acts as an identity with respect to a function, it means that when called with that function and some other value, the result is always equal to that other value. [code]1[/code] is the identity with respect to [code]*[/code] and [code][][/code] is the identity with respect to [code]++[/code]. There are a lot of other monoids to be found in the world of Haskell, which is why the [code]Monoid[/code] type class exists. It's for types which can act like monoids. Let's see how the type class is defined:

The [code]Monoid[/code] type class is defined in [code]import Data.Monoid[/code]. Let's take some time and get properly acquainted with it.

First of all, we see that only concrete types can be made instances of [code]Monoid[/code], because the [code]m[/code] in the type class definition doesn't take any type parameters. This is different from [code]Functor[/code] and [code]Applicative[/code], which require their instances to be type constructors which take one parameter.

The first function is [code]mempty[/code]. It's not really a function, since it doesn't take parameters, so it's a polymorphic constant, kind of like [code]minBound[/code] from [code]Bounded[/code].  [code]mempty[/code] represents the identity value for a particular monoid.

Next up, we have [code]mappend[/code], which, as you've probably guessed, is the binary function. It takes two values of the same type and returns a value of that type as well. It's worth noting that the decision to name [code]mappend[/code] as it's named was kind of unfortunate, because it implies that we're appending two things in some way. While [code]++[/code] does take two lists and append one to the other, [code]*[/code] doesn't really do any appending, it just multiplies two numbers together. When we meet other instances of [code]Monoid[/code], we'll see that most of them don't append values either, so avoid thinking in terms of appending and just think in terms of [code]mappend[/code] being a binary function that takes two monoid values and returns a third.

The last function in this type class definition is [code]mconcat[/code]. It takes a list of monoid values and reduces them to a single value by doing [code]mappend[/code] between the list's elements. It has a default implementation, which just takes [code]mempty[/code] as a starting value and folds the list from the right with [code]mappend[/code]. Because the default implementation is fine for most instances, we won't concern ourselves with [code]mconcat[/code] too much from now on. When making a type an instance of [code]Monoid[/code], it suffices to just implement [code]mempty[/code] and [code]mappend[/code]. The reason [code]mconcat[/code] is there at all is because for some instances, there might be a more efficient way to implement

[code]mconcat[/code], but for most instances the default implementation is just fine.

Before moving on to specific instances of [code]Monoid[/code], let's take a brief look at the monoid laws. We mentioned that there has to be a value that acts as the identity with respect to the binary function and that the binary function has to be associative. It's possible to make instances of [code]Monoid[/code] that don't follow these rules, but such instances are of no use to anyone because when using the [code]Monoid[/code] type class, we rely on its instances acting like monoids. Otherwise, what's the point? That's why when making instances, we have to make sure they follow these laws:

The first two state that [code]mempty[/code] has to act as the identity with respect to [code]mappend[/code] and the third says that [code]mappend[/code] has to be associative i.e. that it the order in which we use [code]mappend[/code] to reduce several monoid values into one doesn't matter. Haskell doesn't enforce these laws, so we as the programmer have to be careful that our instances do indeed obey them.

<h3>Lists are monoids</h3>

Yes, lists are monoids! Like we've seen, the [code]++[/code] function and the empty list [code][][/code] form a monoid. The instance is very simple:

Lists are an instance of the [code]Monoid[/code] type class regardless of the type of the elements they hold. Notice that we wrote [code]instance Monoid [a][/code] and not [code]instance Monoid [][/code], because [code]Monoid[/code] requires a concrete type for an instance. 

Giving this a test run, we encounter no surprises:

Notice that in the last line, we had to write an explicit type annotation, because if we just did [code]mempty[/code], GHCi wouldn't know which instance to use, so we had to say we want the list instance. We were able to use the general type of [code][a][/code] (as opposed to specifying [code][Int][/code] or [code][String][/code]) because the empty list can act as if it contains any type.

Because [code]mconcat[/code] has a default implementation, we get it for free when we make something an instance of [code]Monoid[/code]. In the case of the list, [code]mconcat[/code] turns out to be just [code]concat[/code]. It takes a list of lists and flattens it, because that's the equivalent of doing [code]++[/code] between all the adjecent lists in a list.

The monoid laws do indeed hold for the list instance. When we have several lists and we [code]mappend[/code] (or [code]++[/code]) them together, it doesn't matter which ones we do first, because they're just joined at the ends anyway. Also, the empty list acts as the identity so all is well. Notice that monoids don't require that [code]a `mappend` b[/code] be equal to [code]b `mappend` a[/code]. In the case of the list, they clearly aren't:

And that's okay. The fact that for multiplication [code]3 * 5[/code] and [code]5 * 3[/code] are the same is just a property of multiplication, but it doesn't hold for all (and indeed, most) monoids.

<h3>[code]Product[/code] and [code]Sum[/code]</h3>

We already examined one way for numbers to be considered monoids. Just have the binary function be [code]*[/code] and the identity value [code]1[/code]. It turns out that that's not the only way for numbers to be monoids. Another way is to have the binary function be [code]+[/code] and the identity value [code]0[/code]:

The monoid laws hold, because if you add 0 to any number, the result is that number. And addition is also associative, so we get no problems there. So now that there are two equally valid ways for numbers to be monoids, which way do choose? Well, we don't have to. Remember, when there are several ways for some type to be an instance of the same type class, we can wrap that type in a <i>newtype</i> and then make the new type an instance of the type class in a different way. We can have our cake and eat it too.

The [code]Data.Monoid[/code] module exports two types for this, namely [code]Product[/code] and [code]Sum[/code]. [code]Product[/code] is defined like this:

Simple, just a <i>newtype</i> wrapper with one type parameter along with some derived instances. Its instance for [code]Monoid[/code] goes a little something like this:

[code]mempty[/code] is just [code]1[/code] wrapped in a [code]Product[/code] constructor. [code]mappend[/code] pattern matches on the [code]Product[/code] constructor, multiplies the two numbers and then wraps the resulting number back. As you can see, there's a [code]Num a[/code] class constraint. So this means that

[code]Product a[/code] is an instance of [code]Monoid[/code] for all [code]a[/code]'s that are already an instance of [code]Num[/code]. To use [code]Producta a[/code] as a monoid, we have to do some <i>newtype</i> wrapping and unwrapping:

This is nice as a showcase of the [code]Monoid[/code] type class, but no one in their right mind would use this way of multiplying numbers instead of just writing [code]3 * 9[/code] and [code]3 * 1[/code]. But a bit later, we'll see how these [code]Monoid[/code] instances that may seem trivial at this time can come in handy.

[code]Sum[/code] is defined like [code]Product[/code] and the instance is similar as well. We use it in the same way:

<h3>[code]Any[/code] and [code]All[/code]</h3>

Another type which can act like a monoid in two distinct but equally valid ways is [code]Bool[/code]. The first way is to have the <i>or</i> function [code]||[/code] act as the binary function along with [code]False[/code] as the identity value. The way <i>or</i> works in logic is that if any of its two parameters is [code]True[/code], it returns [code]True[/code], otherwise it returns [code]False[/code]. So if we use [code]False[/code] as the identity value, it will return [code]False[/code] when <i>or</i>-ed with [code]False[/code] and [code]True[/code] when <i>or</i>-ed with [code]True[/code]. The [code]Any[/code] <i>newtype</i> constructor is an instance of [code]Monoid[/code] in this fashion. It's defined like this:

Its instance looks goes like so:

The reason it's called [code]Any[/code] is because [code]x `mappend` y[/code] will be [code]True[/code] if <i>any</i> one of those two is [code]True[/code]. Even if three or more [code]Any[/code] wrapped [code]Bool[/code]s are [code]mappend[/code]ed together, the result will hold [code]True[/code] if any of them are [code]True[/code]:

The other way for [code]Bool[/code] to be an instance of [code]Monoid[/code] is to kind of do the opposite: have [code]&amp;&amp;[/code] be the binary function and then make [code]True[/code] the identity value. Logical <i>and</i> will return [code]True[/code] only if both of its parameters are [code]True[/code]. This is the <i>newtype</i> declaration, nothing fancy:

And this is the instance:

When we [code]mappend[/code] values of the [code]All[/code] type, the result will be [code]True[/code] only if <i>all</i> the values used in the [code]mappend[/code] operations are [code]True[/code]:

Just like with multiplication and addition, we usually explicitly state the binary functions instead of wrapping them in <i>newtype</i>s and then using [code]mappend[/code] and [code]mempty[/code]. [code]mconcat[/code] seems useful for [code]Any[/code] and [code]All[/code], but usually it's easier to use the [code]or[/code] and [code]and[/code] functions, which take lists of [code]Bool[/code]s and return 
[code]True[/code] if any of them are [code]True[/code] or if all of them are [code]True[/code], respectively.

<h3>The [code]Ordering[/code] monoid</h3>

Hey, remember the [code]Ordering[/code] type? It's used as the result when comparing things and it can have three values: [code]LT[/code], [code]EQ[/code] and [code]GT[/code], which stand for <i>less than</i>, <i>equal</i> and <i>greater than</i> respectively:

With lists, numbers and boolean values, finding monoids was just a matter of looking at already existing commonly used functions and seeing if they exhibit some sort of monoid behavior. With [code]Ordering[/code], we have to look a bit harder to recognize a monoid, but it turns out that its [code]Monoid[/code] instance is just as intuitive as the ones we've met so far and also quite useful:

The instance is set up like this: when we [code]mappend[/code] two [code]Ordering[/code] values, the one on the left is kept, unless the value on the left is [code]EQ[/code], in which case the right one is the result. The identity is [code]EQ[/code]. At first, this may seem kind of arbitrary, but it actually resembles the way we alphabetically compare words. We compare the first two letters and if they differ, we can already decide which word would go first in a dictionary. However, if the first two letters are equal, then we move on to comparing the next pair of letters and repeat the process. 

For instance, if we were to alphabetically compare the words [code]"ox"[/code] and [code]"on"[/code], we'd first compare the first two letters of each word, see that they are equal and then move on to comparing the second letter of each word. We see that [code]'x'[/code] is alphabetically greater than [code]'n'[/code], and so we know how the words compare. To gain some intuition for [code]EQ[/code] being the identity, we can notice that if we were to cram the same letter in the same position in both words, it wouldn't change their alphabetical ordering. [code]"oix"[/code] is still alphabetically greater than and [code]"oin"[/code].

It's important to note that in the [code]Monoid[/code] instance for [code]Ordering[/code], [code]x `mappend` y[/code] doesn't equal [code]y `mappend` x[/code]. Because the first parameter is kept unless it's [code]EQ[/code], [code]LT `mappend` GT[/code] will result in [code]LT[/code], whereas [code]GT `mappend` LT[/code] will result in [code]GT[/code]:

OK, so how is this monoid useful? Let's say you were writing a function that takes two strings, compares their lengths, and returns an [code]Ordering[/code]. But if the strings are of the same length, then instead of returning [code]EQ[/code] right away, we want to compare them alphabetically. One way to write this would be like so:

We name the result of comparing the lengths [code]a[/code] and the result of the alphabetical comparison [code]b[/code] and then if it turns out that the lengths were equal, we return their alphabetical ordering.

But by employing our understanding of how [code]Ordering[/code] is a monoid, we can rewrite this function in a much simpler manner:

We can try this out: 

Remember, when we use [code]mappend[/code], its left parameter is always kept unless it's [code]EQ[/code], in which case the right one is kept. That's why we put the comparison that we consider to be the first, more important criterion as the first parameter. If we wanted to expand this function to also compare for the number of vowels and set this to be the second most important criterion for comparison, we'd just modify it like this:


We made a helper function, which takes a string and tells us how many vowels it has by first filtering it only for letters that are in the string [code]"aeiou"[/code] and then applying [code]length[/code] to that.


Very cool. Here, we see how in the first example the lengths are found to be different and so [code]LT[/code] is returned, because the length of [code]"zen"[/code] is less than the length of [code]"anna"[/code]. In the second example, the lengths are the same, but the second string has more vowels, so [code]LT[/code] is returned again. In the third example, they both have the same length and the same number of vowels, so they're compared alphabetically and [code]"zen"[/code] wins.

The [code]Ordering[/code] monoid is very cool because it allows us to easily compare things by many different criteria and put those criteria in an order themselves, ranging from the most important to the least.

<h3>[code]Maybe[/code] the monoid</h3>

Let's take a look at the various ways that [code]Maybe a[/code] can be made an instance of [code]Monoid[/code] and what those instances are useful for.

One way is to treat [code]Maybe a[/code] as a monoid only if its type parameter [code]a[/code] is a monoid as well and then implement [code]mappend[/code] in such a way that it uses the [code]mappend[/code] operation of the values that are wrapped with [code]Just[/code]. We use [code]Nothing[/code] as the identity, and so if one of the two values that we're [code]mappend[/code]ing is [code]Nothing[/code], we keep the other value. Here's the instance declaration:


Notice the class constraint. It says that [code]Maybe a[/code] is an instance of [code]Monoid[/code] only if [code]a[/code] is an instance of [code]Monoid[/code]. If we [code]mappend[/code] something with a [code]Nothing[/code],the result is that something. If we [code]mappend[/code] two [code]Just[/code] values, the contents of the [code]Just[/code]s get [code]mappended[/code] and then wrapped back in a [code]Just[/code]. We can do this because the class constraint ensures that the type of what's inside the [code]Just[/code] is an instance of [code]Monoid[/code].


This comes in use when you're dealing with monoids as results of computations that may have failed. Because of this instance, we don't have to check if the computations have failed by seeing if they're a [code]Nothing[/code] or [code]Just[/code] value; we can just continue to treat them as normal monoids.

But what if the type of the contents of the [code]Maybe[/code] aren't an instance of [code]Monoid[/code]? Notice that in the previous instance declaration, the only case where we have to rely on the contents being monoids is when both parameters of [code]mappend[/code]are [code]Just[/code] values. But if we don't know if the contents are monoids, we can't use [code]mappend[/code] between them, so what are we to do? Well, one thing we can do is to just discard the second value and keep the first one. For this, the [code]First a[/code]type exists and this is its definition:

We take a [code]Maybe a[/code] and we wrap it with a <i>newtype</i>. The [code]Monoid[/code] instance is as follows:

Just like we said. [code]mempty[/code] is just a [code]Nothing[/code] wrapped with the [code]First[/code] <i>newtype</i> constructor. If [code]mappend[/code]'s first parameter is a [code]Just[/code] value, we ignore the second one. If the first one is a [code]Nothing[/code], then we present the second parameter as a result, regardless of whether it's a [code]Just[/code] or a [code]Nothing[/code]:

[code]First[/code] is useful when we have a bunch of [code]Maybe[/code] values and we just want to know if any of them is a [code]Just[/code]. The [code]mconcat[/code] function comes in handy:

If we want a monoid on [code]Maybe a[/code] such that the second parameter is kept if both parameters of [code]mappend[/code] are [code]Just[/code] values, [code]Data.Monoid[/code] provides a the [code]Last a[/code] type, which works like [code]First a[/code], only the last non-[code]Nothing[/code] value is kept when [code]mappend[/code]ing and using [code]mconcat[/code]:


<h3>Using monoids to fold data structures</h3>

One of the more interesting ways to put monoids to work is to make them help us define folds over various data structures. So far, we've only done folds over lists, but lists aren't the only data structure that can be folded over. We can define folds over almost any data structure. Trees especially lend themselves well to folding.

Because there are so many data structures that work nicely with folds, the [class]Foldable[/code] type class was introduced. Much like [code]Functor[/code] is for things that can be mapped over, [code]Foldable[/code] is for things that can be folded up! It can be found in [code]Data.Foldable[/code] and because it export functions whose names clash with the ones from the [code]Prelude[/code], it's best imported qualified (and served with basil):


To save ourselves precious keystrokes, we've chosen to import it qualified as [code]F[/code]. Alright, so what are some of the functions that this type class defines? Well, among them are [code]foldr[/code], [code]foldl[/code], [code]foldr1[/code] and [code]foldl1[/code]. Huh? But we already know these functions, what's so new about this? Let's compare the types of [code]Foldable[/code]'s [code]foldr[/code] and the [code]foldr[/code] from the [code]Prelude[/code] to see how they differ:

Ah! So whereas [code]foldr[/code] takes a list and folds it up, the [code]foldr[/code] from [code]Data.Foldable[/code] accepts any type that can be folded up, not just lists! As expected, both [code]foldr[/code] functions do the same for lists:

Okay then, what are some other data structures that support folds? Well, there's the [code]Maybe[/code] we all know and love!

But folding over a [code]Maybe[/code] value isn't terribly interesting, because when it comes to folding, it just acts like a list with one element if it's a [code]Just[/code] value and as an empty list if it's [code]Nothing[/code]. So let's examine a data structure that's a little more complex then.

Remember the tree data structure from the <a href="making-our-own-types-and-typeclasses#recursive-data-structures">Making Our Own Types and Typeclasses</a> chapter? We defined it like this:

We said that a tree is either an empty tree that doesn't hold any values or it's a node that holds one value and also two other trees. After defining it, we made it an instance of [code]Functor[/code] and with that we gained the ability to [code]fmap[/code] functions over it. Now, we're going to make it an instance of [code]Foldable[/code] so that we get the abilty to fold it up. One way to make a type constructor an instance of [code]Foldable[/code] is to just directly implement [code]foldr[/code] for it. But another, often much easier way, is to implement the [code]foldMap[/code] function, which is also a part of the [code]Foldable[/code] type class. The [code]foldMap[/code] function has the following type:

Its first parameter is a function that takes a value of the type that our foldable structure contains (denoted here with [code]a[/code]) and returns a monoid value. Its second parameter is a foldable structure that contains values of type [code]a[/code]. It maps that function over the foldable structure, thus producing a foldable structure that contains monoid values. Then, by doing [code]mappend[/code] between those monoid values, it joins them all into a single monoid value. This function may sound kind of odd at the moment, but we'll see that it's very easy to implement. What's also cool is that implementing this function is all it takes for our type to be made an instance of [code]Foldable[/code]. So if we just implement [code]foldMap[/code] for some type, we get [code]foldr[/code] and [code]foldl[/code] on that type for free!

This is how we make [code]Tree[/code] an instance of [code]Foldable[/code]:


We think like this: if we are provided with a function that takes an element of our tree and returns a monoid value, how do we reduce our whole tree down to one single monoid value? When we were doing [code]fmap[/code] over our tree,we applied the function that we were mapping to a node and then we recursively mapped the function over the left sub-tree as well as the right one. Here, we're tasked with not only mapping a function, but with also joining up the results into a single monoid value by using [code]mappend[/code]. First we consider the case of the empty tree — a sad and lonely tree that has no values or sub-trees. It doesn't hold any value that we can give to our monoid-making function, so we just say that if our tree is empty, the monoid value it becomes is [code]mempty[/code].

The case of a non-empty node is a bit more interesting. It contains two sub-trees as well as a value. In this case, we recursively [code]foldMap[/code] the same function [code]f[/code] over the left and the right sub-trees. Remember, our [code]foldMap[/code] results in a single monoid value. We also apply our function [code]f[/code] to the value in the node. Now we have three monoid values (two from our sub-trees and one from applying [code]f[/code] to the value in the node) and we just have to bang them together into a single value. For this purpose we use [code]mappend[/code], and naturally the left sub-tree comes first, then the node value and then the right sub-tree.

Notice that we didn't have to provide the function that takes a value and returns a monoid value. We receive that function as a parameter to [code]foldMap[/code] and all we have to decide is where to apply that function and how to join up the resulting monoids from it.

Now that we have a [code]Foldable[/code] instance for our tree type, we get [code]foldr[/code] and [code]foldl[/code] for free! Consider this tree:

It has [code]5[/code] at its root and then its left node is has [code]3[/code] with [code]1[/code] on the left and [code]6[/code] on the right. The root's right node has a [code]9[/code] and then an [code]8[/code] to its left and a [code]10[/code] on the far right side. With a [code]Foldable[/code] instance, we can do all of the folds that we can do on lists:

And also, [code]foldMap[/code] isn't only useful for making new instances of [code]Foldable[/code]; it comes in handy for reducing our structure to a single monoid value. For instance, if we want to know if any number in our tree is equal to [code]3[/code], we can do this:

Here, [code]\x -&gt; Any $ x == 3[/code] is a function that takes a number and returns a monoid value, namely a [code]Bool[/code] wrapped in [code]Any[/code]. [code]foldMap[/code] applies this function to every element in our tree and then reduces the resulting monoids into a single monoid with [code]mappend[/code]. If we do this:

All of the nodes in our tree would hold the value [code]Any False[/code] after having the function in the lambda applied to them. But to end up [code]True[/code], [code]mappend[/code] for [code]Any[/code] has to have at least one [code]True[/code] value as a parameter. That's why the final result is [code]False[/code], which makes sense because no value in our tree is greater than [code]15[/code].

We can also easily turn our tree into a list by doing a [code]foldMap[/code] with the [code]\x -&gt; [x][/code] function. By first projecting that function onto our tree, each element becomes a singleton list. The [code]mappend[/code] action that takes place between all those singleton list results in a single list that holds all of the elements that are in our tree:

What's cool is that all of these trick aren't limited to trees, they work on any instance of [code]Foldable[/code].