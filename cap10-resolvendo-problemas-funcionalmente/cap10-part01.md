Resolvendo Problemas Funcionalmente
===================================

Neste capítulo, iremos dar uma olhada em alguns problemas interessantes e como pensar funcionalmente para resolvê-los tão elegantemente quanto possível. Nós provavelmente não introduziremos nenhum novo conceito, apenas iremos ser flexíveis com nossos recém-adquiridos músculos de Haskell e praticar nossas habilidades de codar. Cada sessão irá apresentar um problema diferente. Primeiro vamos descrever o problema, em seguida iremos tentar encontrar a melhor (ou menos pior) forma de resolve-lo.

** Calculadora de Notação Polonesa Inversa **

Normalmente quando nós escrevemos expressões matemáticas na escola, nós as escrevemos de uma maneira infixada. Por exemplo, nós escrevemos [code]10 - (4 + 3) * 2[/code]. [code]+[/code], [code]*[/code] e [code]-[/code] são operadores infixos, da mesma forma que funções infixadas que nos encontramos em Haskell ([code]+[/code], [code]`elem`[/code], etc.). Isto torna tudo mais prático para nós, como humanos podemos facilmente realizar o parser em nossas mentes apenas olhando qual é a expressão. A desvantagem disto é que nós temos de usar parênteses para denotar a precedência.

[link]Notação Polonesa Inversa[/link] é uma outra forma de escrever matematicamente expressões. Inicialmente isso pode soar estranho, mas esta é atualmente a forma mais fácil de entender e usar porque não é necessário usar parênteses e é muito fácil de colocar isso em uma calculadora. Enquanto muitas das calculadoras modernas usam notação infixa, algumas pessoas continuam usando calculadoras NPI. É assim que a expressão infixa previamente mostrada parece na NPI: [code]10 4 3 + 2 * -[/code]. Como podemos calcular qual o resultado disso? Bem, pense em uma pilha. Quando nós encontramos um operador, pegamos os dois números que estão no topo da pilha (também dizemos que estamos fazendo um <i>pop</i>), usamos o operador, os dois números, por fim colocamos o número resultante de volta à pilha. Quando você encontrar o fim de uma expressão, você estará com um único número se a expressão foi bem formada e este número representa o resultado.

Vamos analisar a expressão [code]10 4 3 + 2 * -[/code] juntos! Primeiro nós colocamos [code]10[/code] na pilha e a pilha agora está assim [code]10[/code]. O próximo item é [code]4[/code] então colocamos ele na pilha também. A pilha agora esta assim [code]10, 4[/code]. Fazemos a mesma coisa com [code]3[/code] e agora a pilha ficou assim [code]10, 4, 3[/code]. Finalmente encontramos uma operação, o nosso conhecido [code]+[/code]! Então pegamos os dois números do topo da pilha (ficando a pilha apenas com o [code]10[/code]), adicionamos estes números e devolvemos o resultado da operação à pilha. A pilha agora esta assim [code]10, 7[/code]. Nós colocamos [code]2[/code] na pilha, e a pilha agora está assim [code]10, 7, 2[/code]. Nós então encontramos um operador novamente, então vamos pegar o [code]7[/code] e [code]2[/code] da pilha, multiplicamos e então colocamos o resultado na pilha. Multiplicando [code]7[/code] e [code]2[/code] produz um [code]14[/code], então a pilha que nós temos agora está assim [code]10, 14[/code]. Finalmente, há um [code]-[/code]. Nós pegamos o [code]10[/code] e o [code]14[/code] da pilha, subtraímos [code]14[/code] de [code]10[/code] e então colocamos o resultado de volta. O número na pilha agora é [code]-4[/code] e como não há mais nenhum número ou operador em nossa expressão, este é nosso resultado!

Agora que nós sabemos como nós podemos calcular qualquer expressão NPI manualmente, vamos pensar como nós podemos fazer uma função de Haskell que pega essa expressão como uma string que contém uma expressão NPI, como [code]"10 4 3 + 2 * -"[/code] e nos devolve o resultado.

Qual pode ser o tipo desta função? Nós queremos pegar uma string como parâmetro e produzir um número como resultado. Então provavelmente será algo como [code]solveRPN :: (Num a) =&gt; String -&gt; a[/code].



<em>Dica útil:</em> realmente ajuda primeiro pensar qual o tipo que a função declarada pode ser antes de nós mesmos escrevermos a implementação. Em Haskell, a declaração do tipo de uma função nos diz muito sobre a função, devido ao sistema de tipos muito forte de Haskell.

Legal. Ao implementar uma solução para um problema em Haskell, é bom pensar um pouco e como você pode fazer isso manualmente e talvez tentar ver se você pode ter alguma inspiração de como fazer. Aqui vemos que nós tratamos cada número ou operador que estava separado por um espaço como um item único. Então isto pode nos ajudar se nós queremos começar a quebrar uma string como [code]"10 4 3 + 2 * -"[/code] em uma lista de itens como [code]["10","4","3","+","2","*","-"][/code].

Logo a seguir, o que vamos fazer com essa lista de itens em nossas cabeças? Nós queremos ir sobre ela da esquerda pra direita e manter uma pilha do que estávamos fazendo. A sentença anterior lembra você de alguma coisa? Lembre-se, na sessão sobre <a href="higher-order-functions/#folds">folds</a>, nós dissemos que uma forma bonita para qualquer função que atravessa os elementos de uma lista da esquerda pra direita ou da direita pra esquerda elemento por elemento e constrói (acumula) algum resultado (se este é um número, uma lista, uma pilha, seja lá o que for) pode ser implementado com um fold.

Neste caso, nós iremos usar um left fold, porque nós iremos sobre a lista da esquerda pra direita. O valor do acumulador irá ser nossa pilha e, portanto, o resultado do nosso fold também será uma pilha, e assim como nós vimos, ela terá apenas um item.

Mais uma coisa para pensar sobre: como iremos representar uma pilha? Eu proponho que nós usemos uma lista. Também proponho de preservarmos o topo da pilha na cabeça dela. Isto porque adicionando na cabeça (começo) da lista é muito mais rápido do que adicionar ao fim dela. Então se nós temos uma pilha, digamos, [code]10, 4, 3[/code], nós iremos representa-lá como uma lista [code][3,4,10][/code].

Agora nós temos informação suficiente para esboçar aproximadamente como será a nossa função. Vamos pegar uma string, como [code]"10 4 3 + 2 * -"[/code] e quebrá-la em uma lista de itens usando [code]words[/code] para obter [code]["10","4","3","+","2","*","-"][/code]. A seguir, nós iremos fazer um left fold sobre esta lista e terminar com uma pilha que tem apenas um elemento, que é [code][-4][/code]. Nós pegamos este único item da lista e então este é o nosso resultado final.

Então aqui está o esqueleto da função:




Nós pegamos a expressão e tornamos ela numa lista de itens. Então nós atravessamos a lista de itens com a função de <i>folding</i>. Pense em um [code][][/code], que representa o acumulador inicial. O acumulador é a nossa pilha, então [code][][/code] representa uma pilha vazia, que é a que nós começamos. Após pegar a pilha final com um único item, nós chamamos [code]head[/code] em nossa lista para pegar este item e então nós aplicamos [code]read[/code].

Então tudo o que nos resta agora é implementar uma função de <i>folding</i> que pega uma pilha, como [code][4,10][/code], e um item, como [code]"3"[/code] e retorna uma nova pilha [code][3,4,10][/code]. Se a pilha é [code][4,10][/code] e o item [code]"*"[/code], então ela irá retornar [code][40][/code]. Mas antes disso, vamos passar nossa função para o <a href="higher-order-functions#composition">estilo de ponto livre</a> porque ela tem muitos parênteses que estão me assustando.



Ah, Vamos lá. Bem melhor. Então, a função de <i>folding</i> irá pegar uma pilha e um item e retornar uma nova pilha. Nós iremos usar casamento de padrões para pegar os principais itens da pilha e para reconhecer os padrões de operadores como [code]"*"[/code] e [code]"-"[/code].





Nós colocamos isso com quatro padrões. Os padrões serão testados do primeiro para o último. Primeiro a função de <i>folding</i> irá ver se o item atual é [code]"*"[/code]. Se ele é, então ele irá pegar uma lista como [code][3,4,9,3][/code] e chamará seus primeiros dois elementos [code]x[/code] e [code]y[/code] respectivamente. Então neste caso, [code]x[/code] pode ser [code]3[/code] e [code]y[/code] pode ser [code]4[/code]. [code]ys[/code] pode ser [code][9,3][/code]. Isto irá retornar uma lista que é como [code]ys[/code], apenas se há [code]x[/code] e [code]y[/code] multiplicado como sua cabeça. Portanto com isso nós iremos retirar os dois números mais ao topo da pilha, multiplica-los e então colocar o resultado de volta na pilha. Se o item não é [code]"*"[/code], o casamento de padrão irá falhar e [code]"+"[/code] será checado, e assim por diante.

Se o item não é nenhum dos operadores, então nós assumimos que ele é uma string que representa um número. Se ele é um número, nós apenas chamamos [code]read[/code] nessa string pra pegar o número dela e retornamos a pilha anterior, mas agora com este número colocado no topo dela.

E é isso! Também notamos que nós adicionamos uma classe extra de restrição a [code]Read a[/code] para a declaração da função, porque nós chamamos [code]read[/code] em nossa string para pegar um número. Então esta declaração significa que o resultado pode ser apenas do tipo que é parte das classes [code]Num[/code] e [code]Read[/code] (como [code]Int[/code], [code]Float[/code], etc.).

Para a lista de itens [code]["2","3","+"][/code], nossa função irá começar atravessando a partir da esquerda. A pilha inicial será [code][][/code]. Ela irá chamar a função de <i>folding</i> com [code][][/code] como uma pilha (acumulador) e [code]"2"[/code] como o item. Como este item não é um operador, ele será lido com [code]read[/code] e adicionado ao início de [code][][/code]. Então a nova pilha agora será [code][2][/code] e a função de <i>folding</i> será chamada com [code][2][/code] como pilha e [code]["3"][/code] como um item, produzindo uma nova pilha [code][3,2][/code]. Então, ela é chamada pela terceira vez com [code][3,2][/code] como a pilha e [code]"+"[/code] como o item. Isto porque estes dois números precisam ser retirados da pilha, somados e então o resultado posto de volta. A pilha final é [code][5][/code], que é o número que nós retornamos.

Vamos brincar com a nossa função: 





Legal, ela funciona! Uma coisa boa sobre nossa função é que ela pode ser facilmente modificada para suportar vários outro operadores. Eles sempre tem de ser operadores binários. Por exemplo, nós podemos criar um operador [code]"log"[/code] que apenas retira um número da pilha e coloca de volta o seu logaritmo. Nós podemos criar operadores ternários que retiram três números da pilha e colocam o resultado de volta ou operações como [code]"sum"[/code] que retiram todos os números e coloca de volta a sua soma.

Vamos modificar nossa função para pegar mais algumas operações. Por causa da simplicidade, nós iremos alterar a declaração do seu tipo para retornar um número do tipo [code]Float[/code].




Wow, maravilhoso! [code]/[/code] é certamente divisão e [code]**[/code] é a exponenciação de ponto flutuante. Com o operador de logaritmo, nós apenas casamos o padrão com um único elemento e o resto da pilha porquê apenas precisamos de um elemento para calcular um logaritmo natural. Com o operador <i>sum</i>, apenas retornamos a pilha que tínhamos com um elemento que é a soma de todos os elementos que estavam presentes na pilha.




Note que nós podemos incluir números de ponto flutuante em nossas expressões porque [code]read[/code] sabe o como ler eles.



Eu penso que criar uma função que pode calcular arbitrariamente expressões NPI de ponto flutuante e que tem a opção de ser facilmente estendida em 10 linhas é algo muito impressionante.

Uma coisa para notar sobre a nossa função é que ela não é realmente tolerante a falhas. Quando dada uma entrada que não faz sentido, ela irá apenas quebrar tudo. Nós vamos criar uma versão tolerante a falhas com esta declaração de tipo [code]solveRPN :: String -&gt; Maybe Float[/code] quando nós aprendermos monads (elas não são assustadoras, confie em mim!). Nós podemos fazer uma coisa agora, mas ela pode ser um pouco tediosa porque pode envolver uma serie de checagens para [code]Nothing[/code] em cada passo. Se você está se sentindo preparado para o desafio, você pode seguir em frente e tentar! <i>Dica:</i> você pode usar [code]reads[/code] para ver se a leitura foi feita com sucesso ou não.