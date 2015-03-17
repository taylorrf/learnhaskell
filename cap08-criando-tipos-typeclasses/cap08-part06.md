Estruturas de dados recursivas
==============================

Como nós vimos, um construtor em um tipo de dados algébrico pode ter muitos (ou nenhum) campos e cada um pode ser de algum tipo concreto. Com isso em mente, nós podemos fazer construtores cujo tipo dos campos são do mesmo tipo! Usando isso, nós podemos criar tipos de dados recursivos, onde o valor de um tipo contém valores daquele tipo, que por sua vez contém mais valores do mesmo tipo, e assim por diante.

Pense na lista: [code][5][/code]. Isso é apenas açúcar sintático para [code]5:[][/code]. Do lado esquerdo do [code]:[/code], há um valor e do lado direito, há uma lista. E neste caso, há uma lista vazia. E agora sobre a lista [code][4,5][/code]? Bem, isso é desaçucarado para [code]4:(5:[])[/code]. Olhando para o primeiro [code]:[/code], nós vemos que isso também tem um elemento do lado esquerdo e uma lista ([code]5:[][/code]) do lado direito. O mesmo vale para uma lista como [code]3:4:5:6:[][/code] (pois [code]:[/code] é associativo a direita) ou [code][3,4,5,6][/code].

Nós podemos dizer que a lista pode ser uma lista vazia ou ela pode ser um elemento unido com um [code]:[/code] com outra lista (que pode ser outra lista vazia ou não).

Então vamos usar tipos de dados algébricos para implementar nossa própria lista!


A leitura é quase como a da nossa definição de lista de um dos parágrafos anteriores. Isto é ou uma lista vazia ou a combinação de uma cabeça com algum valor e uma lista. Se você está confuso sobre isso, você pode achar mais fácil de entender a sintaxe dos registros.



Você também pode estar confuso com este construtor aqui [code]Cons[/code]. <i>cons</i> é outra palavra para [code]:[/code]. Você vê, em listas, [code]:[/code] é atualmente um construtor que pega dois valores e outra lista e retorna uma lista. Nós já podemos usar o nosso novo tipo de lista! Em outras palavras, isto tem dois campos. Um campo é do tipo de [code]a[/code] e o outro é do tipo de [code][a][/code].



Nós chamamos nosso construtor [code]Cons[/code] de uma maneira infixada então você pode ver que ele é como [code]:[/code]. [code]Empty[/code] é como [code][][/code] e [code]4 `Cons` (5 `Cons` Empty)[/code] é como [code]4:(5:[])[/code].

Nós podemos definir funções para serem infixadas automaticamente, tornando-as compostas apenas por caracteres especiais. Nós também podemos fazer o mesmo com construtores, já que eles são apenas funções que retornam um tipo de dados. Veja isso então.



Primeiramente, nós notamos uma nova construção sintática, as declarações de precedência. Quando nós definimos funções como operadores, nós podemos usar isso para dar uma precedência (mas não precisamos). Um estado de precedência é como hermeticamente associar um operador para ser ou associativo a esquerda ou associativo a direita. Por exemplo, [code]*[/code] tem forma de precedência [code]infixl 7 *[/code] e [code]+[/code] tem forma de precedência [code]infixl 6[/code]. Isto significa que eles são ambos associativos a esquerda ([code]4 * 3 * 2[/code] é [code](4 * 3) * 2[/code]) mas [code]*[/code] associa mais que [code]+[/code], porque ele tem uma precedência maior, então [code]5 * 4 + 3[/code] é [code](5 * 4) + 3[/code].

Por outro lado, nos apenas escrevemos [code]a :-: (List a)[/code] em vez de [code]Cons a (List a)[/code]. Agora nós podemos escrever as listas em nosso tipo de lista assim:



Quando nossos tipos derivam de [code]Show[/code], Haskell permanecerá mostrando ele como se o construtor estivesse na forma de função prefixada, portanto com parênteses em volta do operador (lembre-se, [code]4 + 3[/code] é [code](+) 4 3[/code]).

Vamos criar uma função que junta duas das nossas listas em uma. Assim é como [code]++[/code] é definida para listas normais:



Então nós vamos apenas roubar para nossa própria lista. Nós iremos chamar a função de [code].++[/code].


E vamos ver se isso funciona…


Legal. Isto é legal. Se nós quisermos, nós podemos implementar todas as funções que operam com listas usando nosso próprio tipo de lista.

Note como o padrão casa com [code](x :-: xs)[/code]. Isso funciona porque o casamento de padrão é atualmente um casamento de construtores. Nós podemos casar com [code]:-:[/code] porque ele é um construtor para o nosso tipo de lista e nós também podemos casar com [code]:[/code] porque ele é um construtor para o tipo de lista padrão. O mesmo ocorre com [code][][/code]. Porque o casamento de padrão funciona (apenas) em construtores, nós podemos também casar para coisas como, construtores normalmente prefixados ou coisas como [code]8[/code] ou [code]'a'[/code], que são basicamente construtores para tipos números e caracteres, respectivamente.

Agora, nós iremos implementar uma <em>árvore de busca binária</em>. Se você não esta familiarizado com árvores de busca binárias em linguagens como C, aqui está o que elas são: um elemento aponta para dois elementos, um deles está a esquerda e o outro a direita. O elemento da esquerda é menor, o elemento da direita é maior. Cada um destes elementos pode também apontar para dois elementos (ou um, ou nenhum). Na realidade, cada elemento tem até duas sub-árvores. A coisas legal sobre árvores de busca binária é que nós conhecemos que todos os elementos da sub-árvore esquerda são menores, dizendo, 5 tem de ser menor que 5. Elementos da sub-árvore direita serão maiores. Então se nós precisamos buscar por 8 em nossa árvore, nós começamos com 5 e então pelo fato de 8 ser maior que 5, nós iremos para a direita. Nós estamos agora no 7 e porque 8 é maior que 7, nós vamos para a direita novamente. E nós então encontramos nosso elemento em três etapas! Agora se isto for uma lista normal (ou uma árvore, mas não balanceada), podem ser necessários sete etapas em vez de três para ver se o 8 está lá.

Conjuntos e mapeamentos de [code]Data.Set[/code] e [code]Data.Map[/code] são implementados usando árvores, em vez de árvores de busca binária normais, eles usam árvores de busca binária balanceadas, que estão sempre balanceadas. Mas por enquanto, nós iremos apenas implementar uma árvore de busca binária normal.

Aqui está o que nós iremos dizer: uma árvore é ou uma árvore vazia ou é um elemento que contém algum valor e duas árvores. Soa perfeitamente bem para um tipo de dados algébrico!



Ok, tudo bem, isso é bom. Em vez de manualmente construir uma árvore, nós iremos criar uma função que pega uma árvore e um elemento e insere um elemento nela. Nós fazemos isso pela comparação do valor que nós queremos inserir com o nó raiz e então se ele é menor, nós iremos para a esquerda, se ele é maior, nós iremos para direita. Nós fazemos o mesmo para cada nó subsequente até encontrar uma árvore vazia. Um vez que nos encontramos uma árvore vazia, nós apenas inserimos um nó com o valor em vez de uma árvore vazia.


Em linguagens como C, nós fazemos isso pela modificação de ponteiros e valores dentro da árvore. Em Haskell, nós não podemos realmente modificar nossa árvore, então nós temos de fazer uma nova sub-árvore cada vez que nós decidimos ir para esquerda ou direita e no fim da função de inserção nós retornamos uma árvore completamente nova, porque Haskell realmente não tem um conceito de ponteiros, apenas valores. Por isso, o tipo de nossa função de inserção é algo como [code]a -&gt; Tree a - &gt; Tree a[/code]. Ela pega um elemento e uma árvore e retorna uma nova árvore que tem o elemento inserido. Isto pode parecer como algo ineficiente, mas a avaliação preguiçosa cuida do problema.

Então, aqui há duas funções. Uma é uma função utilitária para fazer uma árvore monódica (uma árvore com apenas um nó) e uma função que insere um elemento na árvore.



A função [code]singleton[/code] é apenas um atalho para fazer um nó que tem alguma coisa e então duas sub-árvores vazias. Em nossa função de inserção, nós primeiro temos uma condição de limites como padrão. Se alcançarmos uma sub-árvore vazia, isso significa que nós estamos onde queremos e em vez de uma árvore vazia, nós pomos uma árvore monódica com nosso elemento. Se nós não estamos inserindo em uma árvore vazia, então nós temos de checar algumas coisas. Primeiro, se o elemento que nós estamos inserindo é igual ao elemento da raiz, apenas retornamos uma árvore que é ela mesma. Se ele é menor, retornamos uma árvore que tem o mesmo valor na raiz, a mesma sub-árvore a direita, mas em vez de sua sub-árvore esquerda, nós pomos uma árvore que tem nosso valor inserido nela. Da mesma forma (mas de forma contrária) fazemos isso se o elemento é maior que o elemento da raiz.

Em seguida, nós iremos criar uma função que checa se algum elemento está na árvore. Primeiro, vamos definir uma condição de limite. Se nós estamos buscando por um elemento em uma árvore vazia, então é certo que ele não está ali. Ok. Note como isto é o mesmo que uma condição de limites quando buscamos por um elemento em listas. Se nós estamos procurando por um elemento e uma lista vazia, ele não está lá. De qualquer maneira, se nós estamos procurando por um elemento em uma árvore que não está vazia, então nós checamos algumas coisas. Se o elemento na raiz do nó é o que nós estamos procurando, maravilha! Se não é, o que fazer então? Bem, nós podemos tirar vantagem do conhecimento de que todos os elementos a esquerda são menores que a raiz do nó. Então se o elemento que nós estamos procurando é menor que a raiz do nó, checamos para ver se ele está na sub-árvore esquerda. Se ele é maior, checamos para ver se ele não está na sub-árvore direita.



Tudo o que tínhamos que fazer era escrever o parágrafo anterior em código. Vamos ter alguma diversão com nossas árvores! Em vez de manualmente construir uma (embora podemos), nós iremos usar um varredor de listas para construir uma árvore a partir de uma lista. Lembre-se, praticamente tudo que atravessa uma lista elemento por elemento e então retorna algum tipo de valor pode ser implementado com um interador de lista (fold)! Nós vamos iniciar com uma árvore vazia e então percorrer a lista da direita elemento por elemento inserindo-os na nossa árvore acumuladora.



Nesse [code]foldr[/code], [code]treeInsert[/code] foi uma função de interação (ela pega uma árvore e uma lista de elementos e produz uma nova árvore) e [code]EmptyTree[/code] foi o acumulador inicial. [code]nums[/code], claro, foi a lista sobre a qual interamos.

Quando nós imprimimos nossa árvore no terminal, ela não é muito legível, mas se nós tentarmos, nós podemos decifrar esta estrutura. Nós vemos que o nó raiz é 5 e então há duas sub-árvores, uma que tem o nó raiz 3 e a outra um 7, e etc.



Verificar se um elemento pertence também funciona perfeitamente. Legal!

Então como você pode ver, estrutura de dados algébricos são um conceito realmente legal e poderosos em Haskell. Nós podemos usa-los para fazer qualquer coisa desde valores booleanos e enumeradores para dias de semana para árvores de busca binária e muito mais!
