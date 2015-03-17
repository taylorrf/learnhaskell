Exceções
========

Todas linguagens tem procedures, funções e pedaços de código que em algum momento falham.
Essas coisas acontecem na vida. Para diferentes linguagens existem diferentes maneiras de lidar
com essas falhas. Em C, normalmente retornamos algum valor anormal (como [code]-1[/code] ou um ponteiro nulo)
para indicar que aquilo que a função esta retornando não deve ser tratado como um valor normal.
Java e C#, por outro lado, preferem usar exceções para lidar com falhas. Quando uma exceção é levantada,
o fluxo de controle pula para algum código que definimos para fazer algum tratamento e talvez 
levantar novamente alguma exceção que nos levará para algum outro código que irá lidar com o erro e 
tomar conta dele. 

Haskell tem um belo sistema de tipos. Tipos de dados algébrico que permitem usarmos tipos como 
[code]Maybe[/code] e [code]Either[/code] em valores que esses tipos vão representar e que podem
ter ou não algum valor. Em C, ao retornar, digamos, [code]-1[/code] para uma falha, é uma convenção
amplamente aceita. Mas ela tem significado apenas para humanos. Se não tomarmos cuidado, podemos 
cair no erro de tratar esses valores anormais como algo normal e causar um caos completo no nosso código.

O sistema de tipos em Haskell nos fornece algo muito mais seguro nesse aspecto. A função 
[code]a -&gt; Maybe b[/code] claramente indica que isso pode produzir um [code]b[/code] envolto por 
um [code]Just[/code] ou pode nos retornar [code]Nothing[/code]. O tipo é diferente de simplesmente 
[code]a -&gt; b[/code] e se tentarmos usar alternadamente essas duas funções, o compilador irá
reclamar para nós.

Apesar de ter tipos expressivos que suportam computações falhas, Haskell ainda tem suporte
para exceções, porque elas fazem muito sentido no contexto de I/O. Um bando de coisas
podem dar errado quando lidamos com o mundo externo porque ele simplesmente não é confiável. 
Por exemplo, quando abrimos um arquivo, um monte de coisas podem dar errado. O arquivo pode estar
bloqueado, ele pode não estar mais lá ou o HD ou alguma outra pode não estar mais disponível. 
Então é interessante ser capaz de pular para uma parte em no nosso código que irá lidar com o erro 
quando ele aparecer.

Beleza, então código I/O (ou código impuro) pode lançar exceções. Isso faz todo sentido. Mas e código puro?
Também pode lançar exceções. Pense sobre as funções [code]div[/code] e [code]head[/code]. 
Elas tem tipos de [code](Integral a) =&gt; a -&gt; a -&gt; a[/code] e [code][a] -&gt; a[/code], 
respectivamente. Sem [code]Maybe[/code] ou [code]Either[/code] no tipo retornado e assim mesmo
ambas podem falhar! [code]div[/code] explode na nossa cara se tentarmos dividir por zero e [code]head[/code]
tem chiliques se dermos uma lista vazia pra ele.

Código puro pode levantar exceções, mas elas apenas podem ser capturas no nível de I/O do nosso código
(quando estamos dentro do bloco <i>do</i> que esta dentro de [code]main[/code]). Isso porque você
não sabe quando (ou se) alguma coisa será avaliada em seu código puro, devido a avaliação preguiçosa e
por não termos uma ordem bem definida de execução, enquanto que códigos I/O tem.

Anteriormente, falamos sobre como devemos gastar o mínimo de tempo possível no parte de I/O do 
nosso programa. A lógica do nosso programa deve residir principalmente dentro das nossas funções puras,
porque seus resultados dependem apenas dos parâmetros com que as funções são chamadas.
Ao lidar com funções puras, você só tem que pensar sobre o que a função retorna, porque ela não
pode fazer nada além disso. Isso torna sua vida muito mais fácil. Apesar de ser necessário ter alguma 
lógica em I/O (como a abertura de arquivos e semelhantes), de preferência elas devem ter apenas
o mínimo possível. Funções puras são preguiçosas por padrão, o que significa que nós não sabemos quando 
ela será avaliada e isso realmente não importa. No entanto, uma vez que as funções puras começarem a 
jogar exceções, será importante quando elas serão avaliadas. É por isso que só podemos pegar exceções 
lançadas a partir de funções puras na parte I/O do nosso código. E isso é bem ruim, porque queremos 
manter a parte de I/O o mais enxuto possível. No entanto, se não pegá-las na parte I/O do nosso código, 
nosso programa irá quebrar. A solução? Não misture exceções e código puro. Tire vantagem de poderoso 
sistema tipo de Haskell e use tipos como [code]Either[/code] e [code]Maybe[/code] para representar os 
resultados que podem falhar.

Esse é o porque que vamos apenas dar uma olhada em como usar exceções I/O por enquanto. Exceções I/O
são exceções que são causadas quando alguma coisa der errado enquanto estivermos nos comunicando 
com o mundo externo em uma ação I/O que faça parte de [code]main[/code]. Por exemplo, 
quando tentamos abrir um arquivo e ele foi deletado ou coisa assim. Dê uma olhada nesse programa que 
abre um arquivo cujo nome é dado através da linha de comando do terminal e nos diz quantas linhas
o arquivo tem.

Um programa super simples. Nós processamos a ação I/O [code]getArgs[/code] e atribuimos a primeira
string da lista que é dada por [code]fileName[/code]. Então damos ao conteúdo do arquivo o nome
de [code]contents[/code]. Finalmente, aplicamos [code]lines[/code] nesse conteúdo para obter uma lista
de linhas para saber então o tamanho dessa lista e devolver isso para [code]show[/code] que terá uma
string representando esse número. Ela funciona conforme o esperado, mas e se acontecer dela receber
o nome de um arquivo que não existe?

Aha, recebemos um erro do GHC, nos dizendo que o arquivo não existe. Nosso programa quebrou. 
E se quisermos imprimir uma bela mensagem informando que o arquivo não existe? Um jeitão de fazer
isso é verificando se o arquivo existe antes de tentar abrir ele usando a função [function]doesFileExist[/code]
do módulo [code]System.Directory[/code].

Fizemos [code]fileExists &lt;- doesFileExist fileName[/code] porque [code]doesFileExist[/code] 
tem o tipo de [code]doesFileExist :: FilePath -&gt; IO Bool[/code], que significa que ele irá retornar 
uma ação I/O que tem o seu resultado representado por um valor boleano que irá nos dizer se o arquivo 
existe. Não podemos simplesmente usar [code]doesFileExist[/code] em um <i>if</i> diretamente.

Outra solução aqui pode ser o uso de exceções. Elas são perfeitamente aceitáveis dentro desse contexto.
Um arquivo não existir é uma exceção a nível de I/O, e pegar isso em I/O é tranquilo e belezinha.

Para lidar com isso usando exceções, podemos tirar vantagem da função [function]catch[/code] de 
[code]System.IO.Error[/code]. O tipo dela é [code]catch :: IO a -&gt; (IOError -&gt; IO a) -&gt; IO a[/code].
Ela recebe dois parâmetros. O primeiro é uma ação I/O. Que pode ser por exemplo uma ação I/O que 
tenta abrir um arquivo. O segundo é um pseudo handler. Se a primeira ação I/O fornecida para o [code]catch[/code]
lançar uma exceção I/O, essa exceção será passada para o handler, que vai decidir o que fazer.
Então o resultado final é uma ação I/O que irá agir de acordo com o primeiro parametro ou que irá fazer 
o que o handler quiser caso a primeira ação I/O levantar uma exceção.

Se você já é familiarizado com os blocos <i>try-catch</i> de linguagens como Java ou Python, então a 
função [code]catch[/code] é semelhante a eles. O primeiro parâmetro é aquela coisa que vamos tentar,
o tipo de coisa que é feito em blocos <i>try</i> de linguagens imperativas. O segundo parâmetro é 
o handler que cuida da exceção, assim como a maioria dos blocos <i>catch</i> que pegam a exceção
que você quer dar uma examinada pra ver o que aconteceu. O handler é envocado se uma exceção é levantada. 

O handler pega um valor do tipo [code]IOError[/code], que é um valor que significa que uma exceção I/O
aconteceu. Isso também carrega infomação em relação ao tipo de exceção que foi levantada.
Como esse tipo é implementado depende da implementação da própria linguagem, que significa que não
podemos inspecionar valores do tipo [code]IOError[/code] por pattern matching, assim 
como não podemos usar pattern match em valores do tipo [code]IO <i>qualquer-coisa</i>[/code].
Podemos usar vários predicados úteis para encontrar coisas sobre valores do tipo [code]IOError[/code] 
que é o que já iremos aprender dentro de alguns instantes.

Vamos colocar então o nosso novo amiguinho [code]catch[/code] pra funcionar!

Primeiramente, notamos que se colocarmos aqueles <i>backticks</i> (pequeno caracter: `) ao redor nós 
podemos usa-lá como uma função infixa, porque ela recebe dois parâmetros. Usando como uma função
infixa ela fica mais legível. Então [code]toTry `catch` handler[/code] é o mesmo que 
[code]catch toTry handler[/code], que encaixa bem com o seu tipo. [code]toTry[/code] é a ação I/O
que nós tentamos executar e [code]handler[/code] é a função que recebe um [code]IOError[/code] e 
retorna uma ação para ser executada no caso de exceção.

Vamos colocar isso pra rodar:

No handler, nós não checamos para ver qual o tipo de [code]IOError[/code] recebemos.
Simplesmente dizemos [code]"Oopps, deu algum problema!"[/code] para qualquer tipo de erro.
Apenas pegar todos os tipos de exceções em um handler é uma má pratica em Haskell assim como é 
também na maioria das outras linguagens. E se acontecer alguma outra exceção que não queremos pegar,
quando nós mesmos interrompendo o programa ou algo assim? Esse é porque que vamos fazer o mesmo 
que fazemos em outras linguagens: verificar qual o tipo de exceção estamos recebendo.
Se for o tipo de exceção que estamos esperando receber, fazemos o que queremos fazer.
Se não for, jogamos essa exceção de volta pro mato. Vamos alterar nosso programa para pegar somente
as exceções causadas por um arquivo não existente. 

Tudo continua igual exceto o handler, que modificamos para pegar somente um certo grupo
de exceções I/O. Aqui nós usamos duas novas funções de [code]System.IO.Error[/code] — 
[function]isDoesNotExistError[/code] e [function]ioError[/code]. [code]isDoesNotExistError[/code] 
é um predicado dos [code]IOError[/code]s, que significa que aquela função vai receber um 
[code]IOError[/code] e retornar um [code]True[/code] ou [code]False[/code], significando que ele
tem um tipo [code]isDoesNotExistError :: IOError -&gt; Bool[/code]. Nós usamos ele na exceção que irá passar para o nosso handler dar uma olhada se tem um erro causado
pelo arquivo não existir. Nós usamos a sintaxe de <a href="syntax-in-functions#guards-guards">guardas</a>
aqui, mas poderiamos também ter usado um <i>if else</i>. Se ela não foi causada porque o arquivo não
existe, nós levantamos novamente a exceção que foi passada pelo handler com a função [code]ioError[/code].
Isso tem o tipo [code]ioError :: IOException -&gt; IO a[/code], que então recebe um [code]IOError[/code]
e executa uma ação I/O que irá levanta-lá. A ação I/O tem o tipo [code]IO a[/code], porque ela 
nunca de fato produz um resultado, então ela pode se comportar como [code]IO <i>anything</i>[/code].

Quando a exceção levantada na ação I/O [code]toTry[/code] que colocamos ao lado do bloco <i>do</i>
não for causada por um arquivo não existente, [code]toTry `catch` handler[/code] irá pegar isso 
e em seguida levanta-la novamente. Muito lega, hein?

Existem vários predicados que atuam em [code]IOError[/code] e se um guarda não avalia-lo como 
[code]True[/code], a avaliação passa para o próximo guarda. Os predicados que atuam em 
[code]IOError[/code] são:

Muitos deles são auto-explicativos. [code]isUserError[/code] avalia para [code]True[/code] 
quando usamos a função [function]userError[/code] para criar uma exceção, que será usada
para criar exceções do nosso código e equipa-lo com uma string. Por exemplo, você pode fazer 
[code]ioError $ userError "remote computer unplugged!"[/code], embora seja preferível que você use
tipos como [code]Either[/code] e [code]Maybe[/code] para expressar a possibilidade de falhas 
ao invés de levantar exceções você mesmo com [code]userError[/code].

Então você pode ter um handler que se parece mais ou menos com isso:


Onde [code]notifyCops[/code] e [code]freeSomeSpace[/code] são ações I/O que definimos.
Certifique-se de levantar novamente as exceções se elas não atenderem a nenhum dos critérios,
caso contrário seu programa irá falhar silenciosamente em alguns casos onde ele não deveria.

[code]System.IO.Error[/code] também exporta funções que nos habilitam a pedir para nossas exceções
por alguns atributos, como onde que a manipulação de um arquivo causou um erro, ou qual o nome 
do arquivo. Os que iniciam com [code]ioe[/code] você pode ver uma 
<a href="http://www.haskell.org/ghc/docs/6.10.1/html/libraries/base/System-IO-Error.html#3">lista completa deles</a> 
na documentação. Digamos que queremos imprimir o nome do arquivo que causou o erro. Não podemos 
imprimir o [code]fileName[/code] que recebemos de [code]getArgs[/code], porque somente o [code]IOError[/code]
foi passado para o handler e o handler não sabe nada além disso. A função depende somente dos 
paramêtros com que ela foi chamada. Esse é o porque podemos usar a função [function]ioeGetFileName[/code],
que tem o tipo [code]ioeGetFileName :: IOError -&gt; Maybe FilePath[/code]. Ela pega um [code]IOError[/code] 
como parâmetro e talvez retorna um [code]FilePath[/code] (que é só um sinônimo para [code]String[/code],
lembre-se, por isso é meio que a mesma coisa). Basicamente o que isso faz é extrair o caminho do arquivo 
do [code]IOError[/code], se ele tiver. Vamos mudar o nosso programa para imprimir o caminho do arquivo
responsável pela exceção ocorrida. 

No guarda onde [code]isDoesNotExistError[/code] é [code]True[/code], nós usamos a expressão <i>case</i> 
para chamar [code]ioeGetFileName[/code] com [code]e[/code] e então casar o padrão contra o valor 
retornado por [code]Maybe[/code]. O uso da expressão <i>case</i> é normalmente usada quando 
queremos casar padrões contra alguma coisa sem provocar uma nova função.

Você não precisa usar um handler para [code]catch[/code] exceto em todas as partes de I/O.
Você pode apenas cobrir certas partes do seu código I/O com [code]catch[/code] ou você pode
cobrir várias delas com [code]catch[/code] e usar diferentes handlers para ele, como por exemplo:

Aqui, [code]toTry[/code] usa [code]handler1[/code] com seu handler e [code]thenTryThis[/code] 
usa [code]handler2[/code]. [code]launchRockets[/code] não é um parâmetro de [code]catch[/code], 
então seja qual for a exceção ele pode lançar o que esta quebrando nosso programa, a não ser que
[code]launchRockets[/code] use [code]catch[/code] internamente para manipular como sua própria exceção.
Claro que code]toTry[/code], [code]thenTryThis[/code] e [code]launchRockets[/code] são ações I/O que 
devem estar grudadas com <i>do</i> e hipoteticamente definidos em outro lugar. Isso é meio que similar
com blocos <i>try-catch</i> de outras linguagens, onde você pode cercar seu programa inteiro com 
um único <i>try-catch</i> ou pode usar uma abordagem mais refinada e usar vários outros em diferentes 
partes do seu código para contralar que tipo de erro aconteceu e onde. 

Agora você sabe como lidar com exceções I/O! Levantar exceções em código puro e trabalhar com elas
não foi coberto aqui, principalmente porque, como dissemos antes, Haskell oferece soluções muito melhores
para indicar erros ao invés de reverte-lo em I/O para captura-los. Mesmo quando colamos ações I/O 
juntas elas podem falhar, eu prefiro ter meu tipo algo como [code]IO (Either a b)[/code], significando
que são ações I/O normais mas o resultado que elas produzem são do tipo [code]Either a b[/code], ou seja,
é tanto [code]Left a[/code] ou [code]Right b[/code].