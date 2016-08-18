Functors redux
==============

Já falamos sobre functors no próprio <a href="making-our-own-types-and-typeclasses#the-functor-typeclass"> capítulo deles</a>. Se você ainda não leu ele, provavelmente deveria dar uma olhada agora, ou talvez
depois quando você tiver um pouco mais de tempo. Ou você pode simplemente fingir que já leu ele.

Ainda assim, aqui vai uma rápida revisão: Functors são coisas mapeaveis, assim como listas, [code]Maybe[/code]s, árvores, e tal. Em Haskell, eles são descritos pela typeclass [code]Functor[/code], que tem apenas um método de typeclass, chamado [code]fmap[/code], que tem o tipo [code]fmap :: (a -&gt; b) -&gt; f a -&gt; f b[/code].
Ele diz: me dê uma função que recebe um [code]a[/code] e que retorne um [code]b[/code] e uma caixa
com um [code]a[/code] (ou um monte deles) dentro dela e eu te darei uma caixa com um [code]b[/code]
(ou um monte deles) dentro dela.

<em>Um conselho de amigo.</em> Muitas vezes a analogia das caixas é usada para nos ajudar a ter
alguma intuição sobre como functors funcionam, e depois, provavelmente vamos usar a mesma analogia
para applicative functors e monads. É uma analogia bacana que ajuda as pessoas a entenderem functors
em um primeiro momento, apenas não leve isso tão ao pé da letra, porque para algumas functors essa
analogia da caixa não se aplica muito bem. Um termo mais correto para definir o que as functors
realmente são seria <i>contexto computacional</i>. O contexto pode ser que a computação pode ter
um valor ou pode ter uma falha ([code]Maybe[/code] e [code]Either a[/code]) ou ele pode ter
mais valores (listas), coisas desse tipo.

Se quisermos fazer de um tipo construtor uma instância de [code]Functor[/code], ele terá que ter um tipo
de [code]* -&gt; *[/code], que significa que ele irá receber exatamente um tipo concreto como
um parâmetro de tipo. Por exemplo, de um [code]Maybe[/code] pode ser feita uma instância porque ele recebe um tipo como parâmetro para produzir um tipo concreto, como [code]Maybe Int[/code] ou
[code]Maybe String[/code]. Se um tipo construtor tem dois parâmetros, como [code]Either[/code], temos
de aplicar parcialmente o tipo construtor até que ele só tenha um parâmetro do tipo. Portanto,
não podemos escrever [code]instance Functor Either where[/code], mas podemos escrever
[code]instance Functor (Either a) where[/code] e, em seguida, se imaginarmos que [code]fmap[/code]
é só para [code]Either a[/code], ele teria uma declaração de tipo de [code]fmap :: (b -&gt; c) -&gt; Either a b -&gt; Either a c[/code]. Como você pode ver, a parte [code]Either a[/code] é fixa, porque [code]Either a[/code] recebe apenas um tipo como parâmetro, ao passo que se [code]Either[/code] recebesse dois então [code]fmap :: (b -&gt; c) -&gt; Either b -&gt; Either c[/code] não iria fazer sentido.

Aprendemos por enquanto como que um monte de tipos (bem, tipos construtores na verdade) são
instâncias de [code]Functor[/code], como [code][][/code], [code]Maybe[/code], [code]Either a[/code]
e o tipo [code]Tree[/code] que nós mesmo fizemos. Dizemos como queremos mapear funções para o nosso próprio bem.
Nesse capítulo, vamos dar uma olhada em mais duas instâncias de functor, chamadas de [code]IO[/code] e
[code](-&gt;) r[/code].

Se algum valor tiver o tipo de, digamos, [code]IO String[/code], isso irá significar que uma ação I/O,
quando executada, irá até o mundo real para trazer alguma string para nós, que será o nosso resultado.
Podemos usar [code]&lt;-[/code] na sintaxe <i>do</i> para atrelar esse resultado a um nome.  Já
mencionamos que ações I/O são como pequenas caixas com perninhas que vão até o mundo real e pegam
algum valor para nós. Nós podemos inspecionar o que elas pegaram, mas depois de inspecionar,
nós temos que devolver o valor de volta ao [code]IO[/code]. Ao pensar nessa analogia da caixa com
pequenas perninhas, nós conseguimos ver como [code]IO[/code] age como um functor.

Vamos ver como [code]IO[/code] é uma instância de [code]Functor[/code]. Quando nós usamos [code]fmap[/code] em uma função numa ação I/O, nós esperamos receber de volta uma ação I/O que faz a mesma coisa, mas que tem a nossa função aplicada sobre seus valores resultantes.


O resultado de mapear alguma coisa sobre uma ação I/O será uma ação I/O, por isso logo de cara
nós usamos a sintaxe <i>do</i> pra colar duas ações e fazer uma nova. Na implementação do [code]fmap[/code],
nós fizemos uma nova ação I/O que primeiro executa a ação I/O original e chama o seu resultado [code]result[/code].
Em seguida, fazemos [code]return (f result)[/code]. [code]return[/code] é, como você já sabe, uma função que ira criar uma ação I/O que nada fará além de apenas apresentar isso como resultado. A ação que o bloco <i>do</i> irá produzir sempre tera o
valor resultante da sua última ação. Por isso que nós criamos uma ação I/O que faz absolutamente nada, que irá apenas apresentar
[code]f result[/code] como o resultado da nova ação I/O.

Podemos brincar um pouco nisso para ganhar alguma intuição. É realmente muito simples. Da uma olhada nesse pedaço de código:

O usuário nos envia um valor (line) e nós devolvemos ele para o usuario ao contrário (com a função <i>reverse</i>).
Veja como podemos re-escrever isso usando [code]fmap[/code]:

Da mesma forma que nós mapeamos com [code]fmap[/code] o [code]reverse[/code] em cima do [code]Just "blah"[/code]
para obtermos [code]Just "halb"[/code], nós podemos também fazer [code]fmap[/code] [code]reverse[/code] sobre o [code]getLine[/code]. Isso porque o [code]getLine[/code] é uma ação I/O do tipo [code]IO String[/code] e mapeando
[code]reverse[/code] sobre ele iremos ter de volta uma ação I/O que irá lá fora no mundo real, pegará uma linha e irá aplicar o
[code]reverse[/code] em cima do resultado. Da mesma forma que podemos aplicar uma função para algo dentro de uma caixa [code]Maybe[/code], nós podemos também aplicar uma função para o que esta dentro de uma caixa [code]IO[/code], que apenas irá no
mundo real para obter alguma coisa. Portanto quando nós associamos isso a um nome usando [code]&lt;-[/code], esse nome irá
refletir um resultado que já tem o [code]reverse[/code] aplicado.

--
A ação I/O [code]fmap (++"!") getLine[/code] se comporta como [code]getLine[/code], simplesmente apresentando sempre junto
ao seu resultado um [code]"!"[/code].

Se olharmos em como o tipo [code]fmap[/code] deve ser caso esteja atrelado ao [code]IO[/code],
ele deverá ser algo como [code]fmap :: (a -&gt; b) -&gt; IO a -&gt; IO b[/code]. Aqui o [code]fmap[/code] pega
uma função, uma ação I/O e retorna uma nova ação I/O da mesma forma que a anterior com a exceção de que a função
será aplicada apenas no seu próprio resultado.

Caso você notar que esta sempre associando um resultado de uma ação I/O a um nome, só para aplicar uma função
nela e chamar alguma outra coisa, considere então usar o [code]fmap[/code], simplesmente porque ele fica
mais bonitinho. Se você quiser aplicar diversas transformações em um dado dentro de uma <i>functor</i>,
você pode declarar sua própria função em um nível de abstração mais alto, criando uma função lambda ou
idealmente, usando composição de função:

Provavelmente você já esta sabendo que [code]intersperse '-' . reverse . map toUpper[/code] é uma função
que recebe uma string, mapeia um [code]toUpper[/code] sobre ela, aplica um [code]reverse[/code] no resultado
e por último aplica sobre esse novo resultado o [code]intersperse '-'[/code]. É o mesmo que escrever
[code](\xs -&gt; intersperse '-' (reverse (map toUpper xs)))[/code], porém mais bonitinho.

Uma outra forma de [code]Functor[/code] que nós estivemos sempre lidando com ela mas que ainda
não sabiamos é que [code](-&gt;) r[/code] é uma [code]Functor[/code]. Provavelmente você esta um
pouco confuso agora, pensando "que diabos esse [code](-&gt;) r[/code] significa"? O tipo de função
[code]r -&gt; a[/code] pode ser reescrito como  [code](-&gt;) r a[/code], da mesma forma que podemos
re-escrever [code]2 + 3[/code] como [code](+) 2 3[/code]. Agora quando olhamos o [code](-&gt;) r a[/code],
nós podemos enchergar o [code](-&gt;)[/code] sobre uma ótica diferente, porque nós vemos ele apenas como um
tipo construtor que pega dois parâmetros, assim como o [code]Either[/code]. Mas lembre-se, nós vimos que
um tipo construtor deve pegar exatamente um tipo de parametro para que ele possa ser ums instância de [code]Functor[/code].
Esse é o porque não podemos fazer com que o [code](-&gt;)[/code] seja uma instância de [code]Functor[/code], porém se
nós aplicarmos parcialmente isso ao [code](-&gt;) r[/code], não nos trará problemas. Se a sintaxe permitir que
tipos contrutores sejam parcialmente aplicados com sessões (da mesma forma que podemos aplicar [code]+[/code] ao fazer [code](2+)[/code],
que é o mesmo que [code](+) 2[/code]), você poderá escrever então [code](-&gt;) r[/code] como [code](r -&gt;)[/code].
O que são funcões <i>functors</i>? Bem, vamos dar uma olhada na implementação que esta em [code]Control.Monad.Instances[/code]

Usualmente marcamos funções que contém qualquer coisa e que retornam qualquer coisa como [code]a -&gt; b[/code].
[code]r -&gt; a[/code] é o mesmo esquema, apenas usamos letras diferentes para o tipo da variável.

Se a sintaxe permitisse, poderia ser escrito como

Porém não permite, então temos que escrever da forma anterior.

Antes de mais nada, vamos pensar a respeito do tipo [code]fmap[/code]. Ele é [code]fmap :: (a -&gt; b) -&gt; f a -&gt; f b[/code].
O que vamos fazer agora é substituir mentalmente todos aqueles [code]f[/code], que é a regra onde a instância do nosso functor trabalha, por [code](-&gt;) r[/code]. Vamos fazer isso para entender como o [code]fmap[/code] se comporta nessa instância em particular. Substituindo temos [code]fmap :: (a -&gt; b) -&gt; ((-&gt;) r a) -&gt; ((-&gt;) r b)[/code]. Agora o que nós fazemos é escrever os tipos [code](-&gt;) r a[/code] e [code](-&gt; r b)[/code] de forma
infixa [code]r -&gt; a[/code] e [code]r -&gt; b[/code], como normalmente fazemos com funções. O que obtemos agora é [code]fmap :: (a -&gt; b) -&gt; (r -&gt; a) -&gt; (r -&gt; b)[/code].

Hmmm beleza. Mapear uma função sobre outra função vai produzir uma função, da mesma forma que mapear uma função
sobre um [code]Maybe[/code] vai produzir um [code]Maybe[/code] e mapear uma função sobre uma lista irá produzir
uma lista. O que, por exemplo, o tipo [code]fmap :: (a -&gt; b) -&gt; (r -&gt; a) -&gt; (r -&gt; b)[/code] nos diz?
Então, perceba que isso recebe uma função a partir de [code]a[/code] para [code]b[/code] e uma função de [code]r[/code]
para [code]a[/code] e retorna uma função a partir de [code]r[/code] para [code]b[/code]. Será que isso te lembra alguma coisa?
Sim! Composição de funções! Nós empilhamos a saída de [code]r -&gt; a[/code] sobre a entrada de [code]a -&gt; b[/code] para ter a
função [code]r -&gt; b[/code], que é exatamente o que composição de funções é. Se você observar como a instância é definida acima,
 vai ver que aquilo é apenas uma composição de função. Outra forma de escrever essa instância pode ser:

Isso nos revela que usar [code]fmap[/code] sobre funções é apenas composição de uma maneira óbvia.
Digite em seu terminal [code]:m + Control.Monad.Instances[/code], já que ai é one a instância é definida e então tente
brincar com mapeamentos sobre funções.

Podemos chamar [code]fmap[/code] como uma função infixa e então a semelhança com o [code].[/code] fica bem clara.
Na segunda linha de entrada, estamos mapeando [code](*3)[/code] sobre [code](+100)[/code], que resulta em
uma função que vai pegar uma entrada, chamar [code](+100)[/code] nela e então chamar [code](*3)[/code]
sobre o resultado. Nós realizamos a chamada dessa função com o [code]1[/code].

E como será que a analogia da caixa se encaixa aqui? Bem, se você forçar bastante a barra, ela se encaixa.
Quando usamos o [code]fmap (+3)[/code] sobre [code]Just 3[/code], é tranquilo de imaginar que o [code]Maybe[/code]
seria tipo uma caixa com algumas coisas dentro sobre as quais iremos aplicar a função [code](+3)[/code]. Mas e quando
nós estamos fazendo um [code]fmap (*3) (+100)[/code]? Nesse caso você pode imaginar a função [code](+100)[/code] como
uma caixa que eventualmente irá conter o resultado. Da mesma forma que uma ação I/O pode ser uma espécia de caixa que
se vai ao mundo real e volta pra gente com o resultado. Usando [code]fmap (*3)[/code] no [code](+100)[/code] vai criar
outra função que se comporta como [code](+100)[/code], apenas antes de produzir um resultado, [code](*3)[/code] será aplicado
no resultado. Agora a gente pode ver como o [code]fmap[/code] age como [code].[/code] para as funções.

O fato de que [code]fmap[/code] é uma composição de funções quando usada em funções não é tão absurdamente
útil no momento, mas ao menos é bem interessante. Isso abre um pouco a nossa mente e nos permite entender
que as coisas se comportam mais como cálculos do que caixas ([code]IO[/code] e [code](-&gt;) r[/code]
podem ser functors). A função começa a ser mapeada sobre o resultado do cálculo no próprio cálculo, porém
o resultado desse cálculo é modificado com a função.

Antes de entramos nas regras que o [code]fmap[/code] deve seguir, vamos pensar sobre o tipo desse [code]fmap[/code] mais uma vez.
O tipo dele é [code]fmap :: (a -&gt; b) -&gt; f a -&gt; f b[/code]. Estamos esquecendo que tem a restrição da classe [code](Functor f) =&gt;[/code],
deixando isso de fora pra simplificar as coisas aqui, como estamos falando sobre functors sabemos o porque deixamos o [code]f[/code] por aqui.
Quando aprendemos pela primeira vez sobre <a href="higher-order-functions#curried-functions">funções curried</a> de alta ordem, aprendemos que
todas funções Haskell na verdade pega apenas um parâmetro. A função [code]a -&gt; b -&gt; c[/code] na verdade só recebe uma único parâmetro do tipo
[code]a[/code] e então retorna uma função [code]b -&gt; c[/code], que recebe só um parâmetro e retorna um [code]c[/code]. É assim se a gente chamar
uma função com alguns poucos parâmetros (parcialmente aplicamos ela), a gente recebe de volta uma função que pega o número dos parâmetros que restaram
(se a gente pensar novamente sobre funções recebendo vários parâmetros). Portanto [code]a -&gt; b -&gt; c[/code] pode ser escrito como [code]a -&gt; (b -&gt; c)[/code], para tornar o rearranjo (<i>currying</i>) mais aparente.

Seguindo a mesma linha de pensamento, se a gente escrever [code]fmap :: (a -&gt; b) -&gt; (f a -&gt; f b)[/code],
 podemos então pensar no [code]fmap[/code] não como uma função que pega uma função e um functor que retorna um functor,
 mas como uma função que pega uma função e retorna uma nova função da mesma forma que a anterior, ela apenas
 pega um functor como um parâmetro e retorna um functor como resultado. Ela pega uma função [code]a -&gt; b[/code]
 e retorna uma função [code]f a -&gt; f b[/code]. Isso é chamado de <i>levantar</i> uma função. Vamos brincar em torno dessa idea
 usando o nosso comando do GHCI [code]:t[/code]:

A expressão [code]fmap (*2)[/code] é uma função que pega um functor [code]f[/code] sob números e retorna um functor sob números.
Esse functor pode ser uma lista, um [code]Maybe [/code], uma [code]Either String[/code], tanto faz. A expressão [code]fmap (replicate 3)[/code]
vai pegar um functor sob qualquer tipo e retornar uma functor sob a lista de elementos daquele tipo.

Quando digo <i>um functor sob números</i>, você deve entender isso como <i>um functor que tem números dentro dele</i>.
O anterior é um pouco mais correto técnicamente, porém o último é mais fácil de entender.

This is even more apparent if we partially apply, say, [code]fmap (++"!")[/code] and then bind it to
a name in GHCI.
You can think of [code]fmap[/code] as either a function that takes a function and a functor and then
maps that function over the functor, or you can think of it as a function that takes a function and
lifts that function so that it operates on functors. Both views are correct and in Haskell, equivalent.
The type [code]fmap (replicate 3) :: (Functor f) =&gt; f a -&gt; f [a][/code] means that the function
will work on any functor. What exactly it will do depends on which functor we use it on.
If we use [code]fmap (replicate 3)[/code] on a list, the list's implementation for [code]fmap[/code]
will be chosen, which is just [code]map[/code]. If we use it on a [code]Maybe a[/code],
it'll apply [code]replicate 3[/code] to the value inside the [code]Just[/code], or if it's
[code]Nothing[/code], then it stays [code]Nothing[/code].

Next up, we're going to look at the <em>functor laws</em>. In order for something to be a functor,
it should satisfy some laws. All functors are expected to exhibit certain kinds of functor-like
properties and behaviors. They should reliably behave as things that can be mapped over.
Calling [code]fmap[/code] on a functor should just map a function over the functor, nothing more.
This behavior is described in the functor laws. There are two of them that all instances of
[code]Functor[/code] should abide by. They aren't enforced by Haskell automatically, so you have
to test them out yourself.

<em>The first functor law states that if we map the [code]id[/code] function over a functor,
the functor that we get back should be the same as the original functor.</em> If we write that a bit
more formally, it means that [law]fmap id = id[/code]. So essentially, this says that if we do
[code]fmap id[/code] over a functor, it should be the same as just calling [code]id[/code] on the
functor. Remember, [code]id[/code] is the identity function, which just returns its parameter unmodified.
It can also be written as [code]\x -&gt; x[/code]. If we view the functor as something that can be
mapped over, the [law]fmap id = id[/code] law seems kind of trivial or obvious.

Let's see if this law holds for a few values of functors.


If we look at the implementation of [code]fmap[/code] for, say, [code]Maybe[/code], we can figure out
why the first functor law holds.

We imagine that [code]id[/code] plays the role of the [code]f[/code] parameter in the implementation.
We see that if wee [code]fmap id[/code] over [code]Just x[/code], the result will be
[code]Just (id x)[/code], and because [code]id[/code] just returns its parameter, we can deduce that
[code]Just (id x)[/code] equals [code]Just x[/code]. So now we know that if we map [code]id[/code]
over a [code]Maybe[/code] value with a [code]Just[/code] value constructor, we get that same value back.

Seeing that mapping [code]id[/code] over a [code]Nothing[/code] value returns the same value is trivial.
So from these two equations in the implementation for [code]fmap[/code], we see that the law
[code]fmap id = id[/code] holds.

<em>The second law says that composing two functions and then mapping the resulting function over a
functor should be the same as first mapping one function over the functor and then mapping the other
one.</em> Formally written, that means that [law]fmap (f . g) = fmap f . fmap g[/code].
Or to write it in another way, for any functor <i>F</i>, the following should hold:
[law]fmap (f . g) F = fmap f (fmap g F)[/code].

If we can show that some type obeys both functor laws, we can rely on it having the same fundamental
behaviors as other functors when it comes to mapping. We can know that when we use [code]fmap[/code]
on it, there won't be anything other than mapping going on behind the scenes and that it will
act like a thing that can be mapped over, i.e. a functor. You figure out how the second law holds
for some type by looking at the implementation of [code]fmap[/code] for that type and then using
the method that we used to check if [code]Maybe[/code] obeys the first law.

If you want, we can check out how the second functor law holds for [code]Maybe[/code].
If we do [code]fmap (f . g)[/code] over [code]Nothing[/code], we get [code]Nothing[/code],
because doing a [code]fmap[/code] with any function over [code]Nothing[/code] returns
[code]Nothing[/code]. If we do [code]fmap f (fmap g Nothing)[/code], we get [code]Nothing[/code],
for the same reason. OK, seeing how the second law holds for [code]Maybe[/code] if it's a
[code]Nothing[/code] value is pretty easy, almost trivial. How about if it's a
[code]Just <i>something</i>[/code] value? Well, if we do [code]fmap (f . g) (Just x)[/code],
we see from the implementation that it's implemented as [code]Just ((f . g) x)[/code],
which is, of course, [code]Just (f (g x))[/code]. If we do [code]fmap f (fmap g (Just x))[/code],
we see from the implementation that [code]fmap g (Just x)[/code] is [code]Just (g x)[/code].
Ergo, [code]fmap f (fmap g (Just x))[/code] equals [code]fmap f (Just (g x))[/code] and from the
implementation we see that this equals [code]Just (f (g x))[/code].

If you're a bit confused by this proof, don't worry. Be sure that you understand how
<a href="higher-order-functions#composition">function composition</a> works. Many times, you can
intuitively see how these laws hold because the types act like containers or functions. You can
also just try them on a bunch of different values of a type and be able to say with some certainty
that a type does indeed obey the laws.

Let's take a look at a pathological example of a type constructor being an instance of the
[code]Functor[/code] typeclass but not really being a functor, because it doesn't satisfy the laws.
Let's say that we have a type:

The C here stands for <i>counter</i>. It's a data type that looks much like [code]Maybe a[/code],
only the [code]Just[/code] part holds two fields instead of one. The first field in the
[code]CJust[/code] value constructor will always have a type of [code]Int[/code], and it will be some
sort of counter and the second field is of type [code]a[/code], which comes from the type parameter
and its type will, of course, depend on the concrete type that we choose for [code]CMaybe a[/code].
Let's play with our new type to get some intuition for it.

If we use the [code]CNothing[/code] constructor, there are no fields, and if we use the
[code]CJust[/code] constructor, the first field is an integer and the second field can be any type.
Let's make this an instance of [code]Functor[/code] so that everytime we use [code]fmap[/code],
the function gets applied to the second field, whereas the first field gets increased by 1.

This is kind of like the instance implementation for [code]Maybe[/code], except that when we do
[code]fmap[/code] over a value that doesn't represent an empty box (a [code]CJust[/code] value),
we don't just apply the function to the contents, we also increase the counter by 1.
Everything seems cool so far, we can even play with this a bit:

Does this obey the functor laws? In order to see that something doesn't obey a law, it's enough to
find just one counter-example.


Ah! We know that the first functor law states that if we map [code]id[/code] over a functor,
it should be the same as just calling [code]id[/code] with the same functor, but as we've seen
from this example, this is not true for our [code]CMaybe[/code] functor. Even though it's part
of the [code]Functor[/code] typeclass, it doesn't obey the functor laws and is therefore not a functor.
If someone used our [code]CMaybe[/code] type as a functor, they would expect it to obey the functor
laws like a good functor. But [code]CMaybe[/code] fails at being a functor even though it pretends
to be one, so using it as a functor might lead to some faulty code. When we use a functor, it
shouldn't matter if we first compose a few functions and then map them over the functor or if we
just map each function over a functor in succession. But with [code]CMaybe[/code], it matters,
because it keeps track of how many times it's been mapped over. Not cool! If we wanted
[code]CMaybe[/code] to obey the functor laws, we'd have to make it so that the [code]Int[/code]
field stays the same when we use [code]fmap[/code].

At first, the functor laws might seem a bit confusing and unnecessary, but then we see that if we
know that a type obeys both laws, we can make certain assumptions about how it will act. If a
type obeys the functor laws, we know that calling [code]fmap[/code] on a value of that type will
only map the function over it, nothing more. This leads to code that is more abstract and extensible,
because we can use laws to reason about behaviors that any functor should have and make functions
that operate reliably on any functor.

All the [code]Functor[/code] instances in the standard library obey these laws, but you can check
for yourself if you don't believe me. And the next time you make a type an instance of
[code]Functor[/code], take a minute to make sure that it obeys the functor laws. Once you've dealt
with enough functors, you kind of intuitively see the properties and behaviors that they have in
common and it's not hard to intuitively see if a type obeys the functor laws. But even without the
intuition, you can always just go over the implementation line by line and see if the laws hold or
try to find a counter-example.

We can also look at functors as things that output values in a context. For instance,
[code]Just 3[/code] outputs the value [code]3[/code] in the context that it might or not output
any values at all. [code][1,2,3][/code] outputs three values—[code]1[/code], [code]2[/code], and
[code]3[/code], the context is that there may be multiple values or no values. The function
[code](+3)[/code] will output a value, depending on which parameter it is given.

If you think of functors as things that output values, you can think of mapping over functors as
attaching a transformation to the output of the functor that changes the value. When we do
[code]fmap (+3) [1,2,3][/code], we attach the transformation [code](+3)[/code] to the output of
[code][1,2,3][/code], so whenever we look at a number that the list outputs, [code](+3)[/code]
will be applied to it. Another example is mapping over functions. When we do
[code]fmap (+3) (*3)[/code], we attach the transformation [code](+3)[/code] to the eventual output
of [code](*3)[/code]. Looking at it this way gives us some intuition as to why using
[code]fmap[/code] on functions is just composition ([code]fmap (+3) (*3)[/code] equals
	[code](+3) . (*3)[/code], which equals [code]\x -&gt; ((x*3)+3)[/code]), because we take a
function like [code](*3)[/code] then we attach the transformation [code](+3)[/code] to its output.
The result is still a function, only when we give it a number, it will be multiplied by three and
then it will go through the attached transformation where it will be added to three.
This is what happens with composition.
