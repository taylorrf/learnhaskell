Alguma altissima-ordem está em ordem
====================================

Funções podem receber funções como parâmetros e também retornar funções. Para ilustrar isto, 
nós vamos fazer uma função que recebe uma função e aplica ela duas vezes em algo!

Antes de mais nada, repare na declaração de tipo. Antes, nós não precisavamos de parênteses porque 
[code]-&gt;[/code] é naturalmente associativa à direita. Entretando, aqui, eles são obrigatórios. 
Eles indicam que o primeiro parâmetro é uma função que recebe algo e retorna esta mesma coisa. 
O segundo parâmetro é algo deste tipo também e o valor de retorno é também do mesmo tipo. 
Nós também poderíamos ler esta declaração de uma forma mais <i>curried</i>, mas para nos poupar 
desta dor de cabeça, nós vamos apenas dizer que esta função recebe dois parâmetros e retorna uma coisa. 
O primeiro parâmetro é uma função (do tipo [code]a -&gt; a[/code]) e o segundo é o próprio [code]a[/code]. 
A função pode ser também [code]Int -&gt; Int[/code] ou [code]String -&gt; String[/code] ou qualquer outra 
coisa. Neste caso, o segundo parâmetro também deverá ser deste tipo.

Nota: De agora em diante, nós diremos que funções recebem vários argumentos, apesar do fato de que cada 
função recebe apenas um parâmetro e retorna uma função parcialmente aplicada até chegarmos à uma função 
que retorna um valor sólido. Então, em nome da simplicidade, diremos que [code]a -&gt; a -&gt; a[/code] recebe 
dois parâmetros, apesar de sabermos o que realmente está ocorrendo por debaixo dos panos.

O corpo da função é bem simples. Apenas usamos o parâmetro [code]f[/code] como uma função, aplicando 
[code]x[/code] à ela ao separá-los com um espaço e então aplicando o resultado a [code]f[/code] novamente. 
De qualque modo, brincando com a função:

Fica evidente o quão legal e útil é a aplicação parcial. Se a nossa função requer que passemos à ela uma 
função que recebe apenas um parâmetro, nós podemos apenas aplicar parcialmente uma função no ponto onde 
se recebe apenas um parâmetro e então passá-lo.

Agora nós vamos usar programação de alta ordem para implementar uma função realmente útil que está na 
biblioteca padrão. Ela se chama [code]zipWith[/code]. Ela recebe uma função e duas listas como 
parâmetros e então junta as duas listas aplicando a função entre os elementos correspondentes. 
Aqui está como nós vamos implementá-la:

Olhe a declaração do tipo. O primeiro parâmetro é uma função que recebe duas coisas e produz uma 
terceira. Elas não precisam ser do mesmo tipo, mas podem. O segundo e o terceiro parâmetros são listas. 
O resultado é também uma lista. O primeiro tem que ser uma lista de [code]a[/code]s, porque a função 
de junção recebe [code]a[/code]s como primeiros argumentos. A segunda tem que ser uma lista de 
[code]b[/code]s, porque o segundo argumento da função é do tipo [code]b[/code]. O resultado é uma 
lista de [code]c[/code]s. Se a declaração do tipo de uma função diz que ela aceita uma função 
[code]a -&gt; b -&gt; c[/code] como parâmetro, ela também aceitará uma função 
[code]a -&gt; a -&gt; a[/code], mas não o contrário! Lembre-se de que, quando você está fazendo funções, 
especialmente as de alta ordem, e você não está seguro sobre o tipo, você pode simplesmente tentar 
omitir a declaração do tipo e então verificar o que o Haskell infere que seja, usando [code]:t[/code].

A ação na função é bem parecida com a [code]zip[/code] normal. As condições limite são as mesmas, 
mas há um argumento extra, qual seja, a função de junção, mas este argumento não importa nas condições 
limite, então usamos um [code]_[/code] para ele. E o corpo da função no último <i>pattern</i> é também 
parecido com [code]zip[/code], mas ele não faz [code](x,y)[/code], e sim [code]f x y[/code]. Uma única 
função de alta ordem pode ser usada para uma enorme variedade de tarefas se é suficientemente geral. 
Aqui está uma pequena demonstração de todas as diferentes coisas que a nossa função [code]zipWith'[/code] 
é capaz de fazer:

Como você pode ver, uma única função de alta ordem pode ser usada de modos muito versáteis. A programação 
imperativa utiliza normalmente coisas como iterações com <i>for</i>, iterações com <i>while</i>, setar 
algo à uma variável, verificar seu estado, e etc., para atingir um certo comportamento e então envolve 
isso em uma interface, como em uma função. Já a programação funcional, utiliza funções de alta ordem 
para abstrair padrões comuns, como examinar duas listas aos pares e fazer algo nestes pares ou receber 
um conjunto de soluções e eliminar as que você não precisa.

Nós vamos implementar outra função que também já está na biblioteca padrão, chamada [code]flip[/code]. 
<i>Flip</i> simplesmente recebe uma função e retorna uma função que é parecida com a função original mas 
cujos primeiros dois argumentos são invertidos. Nós podemos implementá-la assim:

Lendo a declaração do tipo, nós dizemos que ela recebe uma função que recebe um [code]a[/code] e um 
[code]b[/code] e retorna uma função que recebe um [code]b[/code] e um [code]a[/code]. Mas justamente 
porque funções são <i>curried</i> por padrão, o segundo par de parênteses é na verdade desnecessário, 
porque [code]-&gt;[/code] é associativo à direita por padrão. 
[code](a -&gt; b -&gt; c) -&gt; (b -&gt; a -&gt; c)[/code] é o mesmo que 
[code](a -&gt; b -&gt; c) -&gt; (b -&gt; (a -&gt; c))[/code], e que é o mesmo que 
[code](a -&gt; b -&gt; c) -&gt; b -&gt; a -&gt; c[/code]. Nós escrevemos que 
[code]g x y = f y x[/code]. Se isso é verdade, então [code]f y x = g x y[/code] também deve ser, certo? 
Tendo isso em mente, nós podemos definir esta função de uma forma ainda mais simples.

Aqui, nós tiramos vantagem do fato de que funções são <i>curried</i>. Quando nós chamamos 
[code]flip' f[/code] sem os parâmetros [code]x[/code] e [code]y[/code], ela vai retornar uma 
[code]f[/code] que recebe esses dois parâmetros, mas que os chama invertidos. Mesmo que funções 
invertidas sejam normalmente passadas para outras funções, nós tiramos vantagem do <i>currying</i> 
quando fazemos funções de alta ordem pensando adiante e escrevendo como o seu resultado final seria 
se ela fosse chamada totalmente aplicada.