Basicão de Typeclasses
======================

Uma Typeclass (classe de tipos) é como uma interface que define um comportamento. Se um tipo é parte de 
uma typeclass, quer dizer que ela suporta e implementa o comportamento especificado pela classe de tipo. 
Muita gente vinda da orientação a objetos se confunde e acha estar diante de uma classe de OO. 
Bom... não. Você pode pensar que são como as interfaces de Java, mas na verdade são muito melhor.

Qual deve ser o tipo da função [code]==[/code]?

<em>Nota</em>: o operador de igualdade ([code]==[/code]) é uma função. Assim como [code]+[/code], 
[code]*[/code], [code]-[/code], [code]/[/code] e quase todos os outros operadores. Se uma função é 
composta apenas de caracteres especiais, ela é por padrão uma função infixa. Se quisermos verificar o 
seu tipo, passe-a para outra função ou chame-a como função prefixa, colocando-a entre parênteses.


Interessante. Temos algo novo aqui, o símbolo [code]=&gt;[/code]. Tudo antes do símbolo 
[code]=&gt;[/code] é denominado <em>class constraint (restrição de classe)</em>. Podemos ler a declaração 
de tipo anterior assim: a função de igualdade recebe dois argumentos de mesmo tipo e retorna um 
[code]Bool[/code]. Esse tipo deve ser membro da classe [code]Eq[/code] (que é a class constraint).

A typeclass [code]Eq[/code] provê uma interface para o teste de igualdade. Qualquer tipo que faça 
sentido ser verificado por igualdade com outro tipo deve estar na typeclass [code]Eq[/code]. Todos os 
tipos Haskell - exceto os de IO (tipo para lidar com entrada e saída) e funções - fazem parte da 
typeclass [code]Eq[/code].

A função [code]elem[/code] tem o tipo [code](Eq a) =&gt; a -&gt; [a] -&gt; Bool[/code] porque usa o 
operador [code]==[/code] para procurar um determinado elemento em uma dada lista.

Algumas Typeclasses básicas:

[class]Eq[/code] é usado por tipos que suportam teste por igualdade. As funções que fazem parte dela 
implementam [code]==[/code] e [code]/=[/code]. Se existe alguma class constraint de [code]Eq[/code] 
para um tipo variável em uma função, usa o operador [code]==[/code] ou [code]/=[/code] em algum lugar 
de sua definição. Todos os tipos já mencionados (com excessão de funções), são parte de [code]Eq[/code], 
então podem ser testados por igualdade.

[class]Ord[/code] é para tipos que têm ordem.

Todos os tipos já vistos (exceto funções) são parte de [code]Ord[/code]. [code]Ord[/code] engloba 
todas as funções de comparação comuns como [code]&gt;[/code], [code]&lt;[/code], [code]&gt;=[/code] e 
[code]&lt;=[/code]. A função [code]compare[/code] requer dois membros de [code]Ord[/code] de mesmo tipo 
e retorna sua ordenação. [type]Ordering[/code] é uma typeclass que pode ser [code]GT[/code], 
[code]LT[/code] ou [code]EQ[/code], significando <i>maior que</i>, <i>menor que</i> e <i>igual a</i>, 
respectivamente. 

Para ser membro de [code]Ord[/code], um tipo deve ser membro do prestigioso e restrito clube do 
[code]Eq[/code].

Membros do [class]Show[/code] podem ser representados como strings. Todos os tipos cobertos até agora 
(com exceção das funções) são suportados por [code]Show[/code]. A função que lida com a <i>typeclass</i> 
[code]Show[/code] mais usada é a [code]show[/code]. Ela recebe um valor de um que tipo presente em 
[code]Show[/code] e nos mostra esse valor como uma string.

[class]Read[/code] é tipo uma oposição da <i>typeclass</i> [code]Show[/code]. A função [code]read[/code] 
recebe uma string e retorna um tipo membro de [code]Read[/code].

Até agora tudo simples. Todos os tipos já vistos estão nessas classes de tipo. Mas o que acontece ao 
tentarmos [code]read "4"[/code]?


O que o GHCI está tentando nos dizer é que não sabe o que se esperar como retorno. Perceba que nos 
usos anteriores de [code]read[/code] nós sempre fazíamos algo com o resultado. Assim, o GHCI podia 
inferir o tipo esperado de [code]read[/code]. Se usassemos ele como um booleano, ele saberia que deveria 
retornar um [code]Bool[/code]. Mas agora ele só sabe que deve ser algum tipo da classe [code]Read[/code]. 
Vamos dar uma olhada na declaração de tipo de [code]read[/code].


Viu? Ele retorna um tipo parte de [code]Read[/code] mas como não usamos o resultado depois, ele não 
saberá qual tipo será. É por isso que podemos especificar explicitamente <em>type annotations</em> 
(anotações de tipos). <i>Anotações de tipos</i> servem para dizer qual tipo que você quer que uma 
expressão assuma. Fazemos isso adicionando [code]::[/code] no fim da expressão com o tipo desejado. 
Observe:

Na maioria das expressões, o compilador já pode assumir qual deve ser o tipo das expressões. Mas 
acontece dele não saber se deve ser [code]Int[/code] ou [code]Float[/code] para uma expressão como 
[code]read "5"[/code]. Para ter certeza, Haskell deveria primeiro avaliar [code]read "5"[/code]. 
Mas como Haskell é uma linguagem estaticamente tipada, precisa saber o tipo de todas as expressões na 
hora da compilação (ou no caso do GHCI, interpretação). Então dizemos ao Haskell: "Ei, essa expressão 
é desse tipo, caso não saiba!".

Os membros de [code]Enum[/code] são tipos que possuem uma seqüência. A maior vantagem da typeclass 
[code]Enum[/code] é poder ser usada em ranges de listas. Seus tipos têm sucessores e predecessores 
definidos, que podem ser conseguidos pelas funções [code]succ[/code] e [code]pred[/code]. Fazem parte 
dessa classe os tipos: [code]()[/code], [code]Bool[/code], [code]Char[/code], [code]Ordering[/code], 
[code]Int[/code], [code]Integer[/code], [code]Float[/code] e [code]Double[/code].

[class]Bounded[/code] são os tipos que possuem limites - máximo e mínimo.

[code]minBound[/code] e [code]maxBound[/code] são diferenciados por ter tipo 
[code](Bounded a) =&gt; a[/code]. São constantes polimórficas.

Todas tuplas não-vazias também estão em [code]Bounded[/code].

[class]Num[/code] é uma typeclass numérica. Seus membros têm a função de agir como números. Vamos ver 
o tipo de um número.

Parece que todos os números são constantes polimórficas. Elas podem tomas a forma de qualquer tipo 
da typeclass [code]Num[/code].

Esses são os tipos da typeclass [code]Num[/code]. Se verificar o tipo de [code]*[/code], descobrirá 
que ela aceita qualquer número.

Recebe três números do mesmo tipo. É por isso que [code](5 :: Int) * (6 :: Integer)[/code] resultará 
em erro e [code]5 * (6 :: Integer)[/code] funcionará e retornará um [code]Integer[/code], já que 5 
pode tomar a forma de um [code]Int[/code] ou de um [code]Integer[/code].

Para estar em [code]Num[/code], o tipo já deve estar em [code]Show[/code] e [code]Eq[/code].

[class]Integral[/code] também é uma typeclass numérica. Enquanto [code]Num[/code] inclui todos os 
números (reais e inteiros), [code]Integral[/code] apenas inteiros. Essa typeclass é composta por 
[code]Int[/code] e [code]Integer[/code].

[class]Floating[/code] inclui apenas números de ponto flutuante, então são [code]Float[/code] e 
[code]Double[/code].


Uma função muito útil para lidar com números é [function]fromIntegral[/code]. A declaração do seu 
tipo é [code]fromIntegral :: (Num b, Integral a) =&gt; a -&gt; b[/code]. Assim, vemos que ela recebe 
um número inteiro e transforma-o em algo mais genérico. Isso é útil quando você precisa que tipos 
inteiros e ponto flutuante trabalhem juntos. Por exemplo, a função [code]length[/code] tem uma 
declaração de [code]length :: [a] -&gt; Int[/code] ao invés de ter algo mais geral como 
[code](Num b) =&gt; length :: [a] -&gt; b[/code]. Acho que está assim por razões históricas, o que, 
na minha opinião, é besteira. Ainda assim, se tentarmos somar o tamanho de uma lista 
([code]length[/code]) com [code]3.2[/code] teremos um erro, pois não é possível somar um 
[code]Int[/code] com um número de ponto flutuante. Então para contornar, [code]fromIntegral 
(length [1,2,3,4]) + 3.2[/code] funciona perfeitamente.

Note que [code]fromIntegral[/code] tem mais de um class constraint em sua declaração de tipo. 
Como pode ver, isso é válido, desde que estejam separados por vírgulas dentro de parênteses.
