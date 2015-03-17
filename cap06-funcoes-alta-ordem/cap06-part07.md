Composição de funções
=====================

Na matemática, <a href="http://pt.wikipedia.org/wiki/Composi%C3%A7%C3%A3o_de_fun%C3%A7%C3%B5es" target="_blank">composição de funções</a> 
é definida como: [image " (f . g)(x) = f(g(x))"], que significa que compor duas funções produz uma nova 
função, quando chamamos com um parâmetro, digamos, <i>x</i> é o equivalente a chamar <i>g</i> com o 
parâmetro <i>x</i> e então chamar o <i>f</i> com aquele resultado.

Em Haskell, composição de funções é praticamente a mesma coisa. Fazemos uma composição de funções 
com a função [code].[/code], que é definida como:

Atente na declaração de tipo. [code]f[/code] deve ter como parâmetro um valor com o mesmo tipo do 
valor retornado de [code]g[/code]. Assim, a função resultante assume um parâmetro do mesmo tipo 
daquele [code]g[/code] que assume e retorna um valor do mesmo tipo que [code]f[/code] retorna. A 
expressão [code]negate . (* 3)[/code] retorna uma função que pega um número, multiplica ele por 3 e 
então faz a negação.

Um dos usos da composição de funções é fazer funções em tempo de execução para passar para outras 
funções. Claro, nós podemos usar lambdas para isso, mas muitas vezes, composição de funções é mais 
clara e concisa. Digamos que temos uma lista de números e nós queremos então tornar todos em números 
negativos. Uma forma para fazer isto seria pegar cada número com valor absoluto e então negá-lo, como isso: 

Observe a lambda e como ela se parece com o resultado da composição da função. Usando composição de 
função, podemos reescrever isto como:

Fabuloso! Composição de funções é associativo à direita, então nós podemos compor muitas funções ao 
mesmo tempo. A expressão [code]f (g (z x))[/code] é o equivalente a [code](f . g . z) x[/code]. Com 
isso em mente, podemos transformar isto

nisso

Mas e quando funções têm muitos parâmetros? Bem, se você quiser usar na composição de funções, 
terá que aplicar parcialmente cada função pegando apenas um parâmetro. 
[code] sum (replicate 5 (max 6.7 8.9))[/code] pode ser reescrita como 
[code](sum . replicate 5 . max 6.7) 8.9[/code] ou como [code]sum . replicate 5 . max 6.7 $ 8.9[/code]. 
O que acontece aqui é: uma função que pega o resultado de [code]max 6.7[/code] e aplica em 
[code]replicate 5[/code]. Em seguida, a função pega o resultado disso e não a soma do que é criado. 
Finalmente, a função é chamada com [code]8.9[/code]. Mas normalmente, você lê isso como: aplique 
[code]8.9[/code] em [code]max 6.7[/code], então aplique [code]replicate 5[/code] e depois aplique 
[code]sum[/code]. Se você quiser reescrever essa expresão com alguns parênteses usando composição de 
funções, você pode começar colocando o último parâmetro na função mais interna após um [code]$[/code] 
e depois compor todas as outras chamadas de funções, escrevendo sem o último parâmetro e colocando 
pontos entre eles. Se você tiver [code]replicate 100 (product (map (*3) (zipWith max [1,2,3,4,5] 
[4,5,6,7,8])))[/code], você pode escrever isso como [code]replicate 100 . product . map (*3) . 
zipWith max [1,2,3,4,5] $ [4,5,6,7,8][/code]. Se a função terminar com uma árvore de parânteses, são 
grandes as chances de você traduzir em uma composição de função, que terá três operadores de composição.

Outro uso comum da composição de função é definir funções em um estilo chamado de 'ponto livre'. 
Pegue como exemplo esta função que escrevemos anteriormente: 

O [code]xs[/code] é exposto em ambos os lados. Por causa do currying, nós podemos omitir o 
[code]xs[/code] em ambos os lados, porque chamar [code]foldl (+) 0[/code] cria uma função que pega uma 
lista. Escrevendo a função como [code]sum' = foldl (+) 0[/code] é o que se chama de escrever com o 
estilo ponto livre. Como podemos escrever isso no estilo ponto livre?

Não podemos simplesmente se livrar do [code]x[/code] em ambos os lados.  O [code]x[/code] no corpo 
da função esta entre parênteses. [code]cos (max 50)[/code] não faz sentido. Você não obtém o cosseno 
da função. O que podemos fazer é expressão [code]fn[/code] com uma composição de funções.


Excelente! Muitas vezes, o estilo ponto livre é mais legível e conciso, porque ele faz você pensar 
sobre as funções e o que cada função que compõe os resultados fazem ao invés de pensar sobre os dados 
e como eles são embaralhados. Você pode pegar funções simples e usar composição como cola para tornar 
mais complexa. Tanto faz, usar o estilo ponto livre pode se tornar menos legível se a função for muito 
complexa. Por isso que não é recomendado fazer longas composições de funções, apesar de eu me declarar 
culpado por usar composição muitas vezes. O estilo preferencial é usar uma associação <i>let</i> para 
rotular resultados intermediários ou juntar o problema em subproblemas e depois colocá-los juntos para 
que a função faça sentido para que alguém que irá ler ao invés de fazer uma longa cadeia de composições.

Na sessão sobre mapas e filtros, resolvemos um problema encontrando a soma de todos os quadrados 
ímpares que são menores do que 10.000. Veja como fica a solução quando colocamos isso na função.

Sendo um grande fã de composição de função, provavelmente eu teria escrito assim:

No entanto, se tiver alguma chance de outra pessoa ler esse código, eu escreveria então assim:

Eu não iria ganhar nenhuma competição de golfe com esse código, mas qualquer um que ler a função 
provavelmente vai achar mais fácil ler isso do que uma cadeia de composição.