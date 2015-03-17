tipos e algumas classes-foo
===========================

Construtores de tipos pegam outros tipos como parâmetros para eventualmente produzir tipos concretos. Isso meio que me lembra de funções, que pega valores como parâmetros para produzir valores. Nós vimos que esses construtores de tipo podem ser parcialmente aplicados ([code]Either String[/code] é um tipo que pega um tipo e produz um tipo concreto, como [code]Either String Int[/code]), assim como as funções podem. Isso é, de fato, muito interessante. Nessa seção, nós daremos uma olhada na definição formal de como tipos são aplicados a construtores de tipos, assim como nós demos uma olhada em como definir formalmente como valores são aplicados a funções usando declarações de tipo. <em>Você não precisa realmente ler essa seção para continuar sua jornada mágica em Haskell</em> e se você não entender isso, não se preocupe. Porém, entender isso vai te dar um conhecimento muito profundo do sistema de tipos.

Então, valores como [code]3[/code], [code]"YEAH"[/code] ou [code]takeWhile[/code] (funções também são valores, porque nós podemos passar elas como parâmetro) têm cada um seu próprio tipo. Tipos são pequenos rótulos que os valores carregam para que nós possamos pensar a respeito dos valores. Mas os tipos têm seus próprios rótulos, chamados <em>classes</em>. Uma classe é mais ou menos um tipo de tipo. Isso pode soar um pouco estranho e confuso, mas é de fato um conceito muito legal.

O que são classes e pra que elas servem? Bem, vamos examinar a classe de um tipo usando o comando [code]:k[/code] no GHCI.



Uma estrela? Que exótico. O que isso significa? Uma [code]*[/code] significa que o tipo é um tipo concreto. Um tipo concreto é um tipo que não pega nenhum parâmetro de tipo e os valores só podem ter tipos que são tipos concretos. Se eu tiver que ler [code]*[/code] em voz alta (eu nunca tive que fazer isso até hoje), eu diria <i>estrela</i> ou só <i>tipo</i>.

Ok, agora vamos ver qual a classe de [code]Maybe[/code] .



O construtor de tipo [code]Maybe[/code] pega um tipo concreto (como [code]Int[/code]) e depois retorna um tipo concreto como [code]Maybe Int[/code]. E é isso que essa classe nos diz. Assim como [code]Int -&gt; Int[/code] significa que uma função pega um [code]Int[/code] e retorna um [code]Int[/code], [code]* -&gt; *[/code] significa que o construtor de tipos pega um tipo concreto e retorna um tipo concreto. Vamos aplicar o parâmetro de tipo ao [code]Maybe[/code] e ver qual a classe desse tipo.




Exatamente como eu esperava! Nós aplicamos o parâmetro de tipo ao [code]Maybe[/code] e recebemos de volta um tipo concreto (é isso o que [code]* -&gt; *[/code] significa. Um paralelo (embora não seja equivalente, tipos e classes são duas coisas diferentes) a isso é se nós fizermos [code]:t isUpper[/code] and [code]:t isUpper 'A'[/code]. [code]isUpper[/code] tem como tipo [code]Char -&gt; Bool[/code] e [code]isUpper 'A'[/code] tem como tipo [code]Bool[/code], porque seu valor é basicamente [code]False[/code]. Ambos os tipo, porém, têm uma espécie de [code]*[/code].

Nós usamos [code]:k[/code] em um tipo para obter sua classe, assim como nós podemos usar [code]:t[/code] em um valor para obter seu tipo. Como nós dissemos, tipos são os rótulos dos valores e classes são os rótulos dos tipos e existem paralelos entre os dois.

Vamos olhar para outra classe.



Arrá, isso nos diz que [code]Either[/code] pega dois tipos concretos como parâmetros de tipo para produzir um tipo concreto. Isso também se parece com uma declaração de tipo de uma função que pega dois valores e retorna alguma coisa. Construtores de tipo são curriadas (assim como funções), assim nós podemos aplicá-las parcialmente.




Quando nós queríamos fazer de [code]Either[/code] uma parte da typeclass de [code]Functor[/code], nós tínhamos que aplicá-la parcialmente porque [code]Functor[/code] requer tipos que pegam somente um parâmetro enquanto que [code]Either[/code] pega dois. Em outras palavras, [code]Functor[/code] requer tipos da classe [code]* -&gt; *[/code] e então nós teremos que aplicar parcialmente [code]Either[/code] para obter um tipo da classe [code]* -&gt; *[/code] ao invés da sua classe original, [code]* -&gt; * -&gt; *[/code]. Se nós olharmos para a definição de [code]Functor[/code] de novo


nós vemos que a variável de tipo [code]f[/code] é usada como um tipo que pega um tipo concreto para produzir um tipo concreto. Nós sabemos que isso tem que produzir um tipo concreto porque isso é usado como o tipo de um valor em uma função. E disso, nós podemos deduzir que tipos que quiserem ser amigos de [code]Functor[/code] têm que ser da classe [code]* -&gt; *[/code].


Agora, vamos ver algumas classes-foo. Dê uma olhada nessa typeclass que eu vou fazer agora:





Cara, isso parece estranho. Como vamos fazer um tipo que pode ser uma instância dessa estranha typeclass? Bom, vamos olhar no que a sua classe deveria ser. Pelo fato de [code]j a[/code] ser usado como o tipo de um valor que a função [code]tofu[/code] pega como seu parâmetro, [code]j a[/code] tem ser da classe [code]*[/code]. Nós assumimos [code]*[/code] para [code]a[/code] e então nós podemos inferir que [code]j[/code] tem que ser da classe [code]* -&gt; *[/code]. Nós vemos que [code]t[/code] tem que produzir um valor concreto também e que ele pega dois tipos. E sabendo que [code]a[/code] é da classe [code]*[/code] e [code]j[/code] é da classe [code]* -&gt; *[/code], nós inferimos que [code]t[/code] tem que ser da classe [code]* -&gt; (* -&gt; *) -&gt; *[/code]. Então ele pega um tipo concreto ([code]a[/code]), um construtor de tipos que pega um tipo concreto ([code]j[/code]) e produz um tipo concreto. Uau.

OK, vamos fazer um tipo com a classe [code]* -&gt; (* -&gt; *) -&gt; *[/code]. Aqui está uma maneira de fazer isso.




Como nós sabemos que esse tipo é da classe [code]* -&gt; (* -&gt; *) - &gt; *[/code]? Bem, campos em ADTs são feitos para guardar valores, então eles devem ser da classe [code]*[/code], obviamente. Nós assumimos [code]*[/code] para [code]a[/code], o que significa que [code]b[/code] pega um parâmetro de tipo e então sua classe é [code]* -&gt; *[/code]. Agora nós sabemos as classes de ambos [code]a[/code] e [code]b[/code] e como eles são parâmetros para [code]Frank[/code], nós vimos que [code]Frank[/code] é da classe [code]* -&gt; (* -&gt; *) -&gt; *[/code]. O primeiro [code]*[/code] representa [code]a[/code] e o [code](* -&gt; *)[/code] representa [code]b[/code]. Vamos fazer algums valores [code]Frank[/code] e verificar seus tipos.




Humm. Como [code]frankField[/code] tem um tipo da forma [code]a b[/code], seus valores tem que ter tipos que tem uma forma similar. Então eles podem ser [code]Just "HAHA"[/code], que é do tipo [code]Maybe [Char][/code] ou ele pode ter o valor [code]['Y','E','S'][/code], que é do tipo [code][Char][/code] (se nós usássemos nossa própria lista para isso, ele seria do tipo [code]List Char[/code]). E nós vemos que os tipos dos valores de [code]Frank[/code] correspondem com a classe de [code]Frank[/code]. [code][Char][/code] é da classe [code]*[/code] e [code]Maybe[/code] é da classe [code]* -&gt; *[/code]. Para se obter um valor, isso tem que ser um tipo concreto e ainda tem que ser completamente aplicado, pois todo valor de [code]Frank blah blaah[/code] é da classe [code]*[/code].

Tornar [code]Frank[/code] uma instância de [code]Tofu[/code] é bem simples. Nós vemos que [code]tofu[/code] pega um [code]j a[/code] (logo um exemplo de tipo dessa forma seria [code]Maybe Int[/code]) e retorna um [code]t a j[/code]. Logo, se nós trocarmos [code]Frank[/code] por [code]j[/code], o tipo do resultado seria [code]Frank Int Maybe[/code].



Não muito útil, mas nós flexionamos nossos 'músculos de tipo'. Vamos fazer alguns outros tipo-foo. Nós temos esse tipo de dado:



E agora nós queremos fazer dele uma instância de [code]Functor[/code]. [code]Functor[/code] requer tipos da classe [code]* -&gt; *[/code] mas [code]Barry[/code] não parece ser dessa classe. Qual a classe de [code]Barry[/code]? Bem, nós vemos que ele pega três parâmetros de tipo, então ele será [code]something -&gt; something -&gt; something -&gt; *[/code]. É seguro dizer que [code]p[/code] é um tipo concreto e que é da classe [code]*[/code]. Para [code]k[/code], nós assumimos [code]*[/code] e por extensão, [code]t[/code] é da classe [code]* -&gt; *[/code]. Agora vamos apenas trocar essas classes pelo <i>somethings</i> que usamos como placeholders e veremos se é da classe [code](* -&gt; *) -&gt; * -&gt; * -&gt; *[/code]. Vamos checar com o GHCI.



Ah, estávamos certos. Que satisfatório. Agora, para fazer disso uma parte de [code]Functor[/code], nós temos que aplicar parcialmente os dois primeiros parâmetros de tipo e assim só nos resta [code]* -&gt; *[/code]. Isso significa que o começo de uma declaração de instância será: [code]instance Functor (Barry a b) where[/code]. Se nós olharmos para [code]fmap[/code] como se ele fosse feito especificamente para [code]Barry[/code], ele seria do tipo [code]fmap :: (a -&gt; b) -&gt; Barry c d a -&gt; Barry c d b[/code], porque nós só trocamos o [code]f[/code] do [code]Functor[/code] pelo [code]Barry c d[/code]. O terceiro tipo de parâmetro de [code]Barry[/code] terá que mudar e nós vemos que isso está convenientemente no seu campo.


Aqui vamos nós! Nós acabamos de mapear o [code]f[/code] sobre o primeiro campo.

Nessa seção, nós demos uma boa olhada em como parâmetros de tipo funcionam e meio que formalizamos eles com classes, assim como nós formalizamos parâmetros de função com declaração de tipos. Nós vimos que existem paralelos interessantes entre funções e construtores de tipos. Eles são, porém, duas coisas completamente diferentes. Quando trabalhar com Haskell de verdade, você normalmente não vai se confundir com classes e fará inferência de classes na mão como nós fizemos agora. Normalmente, você só tem que aplicar parcialmente seu próprio tipo em [code]* -&gt; *[/code] ou [code]*[/code] quando for fazer uma instância de uma das typeclasses padrão, mas é bom saber como e porquê isso de fato funciona. Também é interessante ver que tipos tem seus próprios pequenos tipos. De novo, você não precisa entender tudo que fizemos aqui para continuar a leitura, mas se você entendeu como classes funcionam, você provavelmente tem uma base muito sólida do sistema de tipos de Haskell.
