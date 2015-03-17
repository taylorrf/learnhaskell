Uma trilha de migalhas de pão
=============================

Então, para focar em um sub-árvore queremos algo melhor do que só uma lista de direções para seguir da raiz da nossa árvore. Será que ajudaria se começássemos a partir da raiz da árvore e percorremos tanto para a esquerda quanto para a direita, um passo decada vez e deixássemos migalhas espalhadas? É isso, quando formos para a esquerda vamos nos lembrar que viemos da esquerda e quando formos para direita vamos lembrar que viemos da direita. Claro, nós podemos tentar isso.

Para representar as migalhas, nós também podemos usar uma lista de [code]Direction[/code] (qual pode ser [code]E[/code] ou [code]D[/code]), só que ao invés de chamarmos de [code]Directions[/code] nós podemos dizer que são [code]Breadcrumbs[/code], pois agora o sentido será o inverso já que estamos deixando a medida que descemos nossa árvore:

Aqui está a função que recebe a árvore e algumas migalhas percorrendo a sub-árvore esquerda enquanto adiciona [code]E[/code] para a cabeça da lista que representa as migalhas:

Nós ignoramos o elemento da raiz e a sub-árvore direita retornando a sub-árvore esquerda juntamente com a lista de migalhas antiga com [code]E[/code] como cabeça da lista. Aqui está a função para ir para direita:

Funciona do mesmo jeito. Vamos usar essas funções para receber nossa [code]freeTree[/code] e ir para direita e para esquerda:

Ok, então agora nós temos a árvore que tem [code]'W'[/code] na raiz, [code]'C'[/code] na raiz da sub-árvore esquerda e [code]'R'[/code] na raiz da sub-arvore direita. As migalhas são [code][E,D][/code], pois primeiro fomos para direita para depois ir para a esquerda.

Para percorrer a árvore de maneira mais clara, nós podemos usar a função [code]-:[/code] que definimos assim:

Essa função nos permite aplicar as funções nos valores primeiramente escrevendo o valor, depois a função [code]-:[/code] e por último a função. Então, ao invés de [code]goRight (freeTree, [])[/code], nós podemos escrever [code](freeTree, []) -: goRight[/code]. Usando isso podemos reescrever o código acima de modo mais evidente de que estamos indo primeiro para direita e depois para esquerda:

- Voltando para cima

E se agora nós quiséssemos voltar para cima na nossa árvore? Por causa das nossas migalhas nós sabemos que a árvore atual é ou a sub-arvore direita ou esquerda de seus pais, mas é só isso. Elas não nos dizem o suficiente sobre o pai da sub-arvore analisada para que nós sejamos capazes de voltar para o elemento pai na árvore. Parece que além da direção que tomamos, uma única migalha também deve conter todos os outros dados que precisamos para voltar a subir. Neste caso, que é o elemento na árvore do pai junto com sua sub-árvore direita.

Em geral uma única migalha deve conter todas as informações necessárias para reconstruir o nó pai. Por isso, devem ter a informação de todos os caminhos que não levam e também deve saber a direção que tomou, mas não deve conter a sub-árvore que estamos atualmente focando. Isso acontece porque nós já temos a sub-árvore no primeiro componente da tupla, então se tivéssemos também nas migalhas nós teríamos informação duplicada.

Vamos modificar nossa migalha para que elas também contenham as informações sobre tudo que anteriormente nós ignoramos quando caminhamos para esquerda e direita. Ao invés de [code]Direction[/code], nós iremos fazer uma novo tipo de dados:

Agora, ao invés de só [code]L[/code], nós temos a [code]LeftCrumb[/code] que também contém o elemento do nó de onde viemos e a árvore direita que não visitamos. Ao invés de [code]D[/code], nós temos [code]RightCrumb[/code], que contém o elemento do nó de onde viemos e a árvore esquerda que nós não visitamos.

Essas migalhas agora contém todas as informações necessárias para recriar a árvore pelo qual nós caminhamos. Ao invés de ser só migalhas comuns, elas agora são mais como disquetes que nós deixamos pelo meio do caminho, pois elas contém mais informações do que só direções que nós tomamos.

Em essência, toda migalha é agora como um nó de árvore com um buraco. Quando nos movemos para o interior da árvore, a migalha carrega toda a informação do nó de onde viemos <i>exceto</i> a sub-árvore que nós escolhemos para focar. Também temos que notar onde o buraco está. Nesse caso da [code]LeftCrumb[/code], nós sabemos que nós viemos da esquerda, então a sub-árvore que está faltando é a esquerda.

Vamos também mudar nosso tipo [code]Breadcrumbs[/code] para um sinônimo que reflita isso:

A seguir, temos que modificar as funções [code]goLeft[/code] e o [code]goRigth[/code] para guardarem as informações sobre os caminhos que nós não fomos nas migalhas, ao invés de ignorar essa informação como fazíamos antes. Aqui está o [code]goLeft[/code]:

Você pode notar que é bastante semelhante ao nosso [code]goLeft[/code] anterior, a diferença está que ao invés de só adicionar [code]L[/code] a cabeça da nossa lista de migalhas, nós adicionamos a [code]LeftCrumb[/code] para que signifique que viemos da esquerda e equipamos a [code]LeftCrumb[/code] com o elemento do nó de que viemos (isso é o [code]x[/code]) e a sub-árvore direita que decidimos não visitar.

Note que essa função assume que a árvore em foco não está [code]Empty[/code]. Uma árvore vazia não possui sub-árvores, então se nós tentarmos ir para esquerda em uma árvore vazia um erro acontecerá pois o caminho do [code]Node[/code] não existe e não existe representação para o valor [code]Empty[/code].

A função [code]goRigth[/code] é similar:

Nós éramos capazes de ir para direita e para esquerda. O que conseguimos agora é a habilidade de ir para trás pois conseguimos relembrar informações sobre os pais dos nós e os caminhos que não visitamos. Aqui está a função [code]goUp[/code]:

Nós estamos focando na árvore [code]t[/code] e checamos qual é a última <i>migalha</i>. Se a última migalha for uma [code]LeftCrumb[/code], então nós construímos uma nova árvore onde nossa árvore [code]t[/code] é a sub-árvore esquerda e nós usamos a informação sobre a sub-árvore direita que nós não visitamos e o elemento para completar o resto do [code]nó[/code]. Como nós voltamos para trás, por assim dizer, e pegamos a última migalha para recriar a árvore-mãe, a nova lista de migalhas não contém a migalha usada.

Note que essa função causa um erro se tentarmos voltar a partir do topo da árvore. Mais tarde iremos usar a monad [code]Maybe[/code] para representar essa possibilidade de falha quando mudamos o foco.

Com um par de [code]Tree a[/code] e [code]Breadcrumbs a[/code], nós temos toda a informação para reconstruir toda a árvore e também temos o foco na sub-árvore. Esse esquema também permiti facilmente mover para cima, direita ou esquerda na árvore. O par que contém a parte em foco da estrutura de dados e seus arredores é chamado de zíper, pois movendo seu foco para cima e para baixo da estrutura de dados lembra o movimento de um zíper de uma calça comum. Então é legal fazer um tipo com esse significado:

Eu preferia chamar esse tipo de [code]Focus[/code] pois faz mais sentido já que estamos focando em uma parte da estrutura de dados, mas o termo zíper é mais amplamente usado para descrever tal configuração, então vou ficar com [code]Zipper[/code].

- Manipulando árvores sobe o foco

Agora que podemos nos mover para cima e para baixo, vamos fazer uma função que modifique um elemento na raiz de uma sub-árvore que o zipper está focado:

Se nós focamos em um nó, modificamos a raiz do elemento com a função 
[code]f[/code]. Se focarmos em uma árvore vazia, deixamos ela como ela é. Agora nós podemos começar com árvore, percorrer qualquer caminho que quisermos e modificar um elemento tudo isso enquanto mantemos o foco naquele elemento podendo facilmente ir para cima ou para baixo. Um exemplo:

Nós vamos para esquerda, depois para direita e depois modificamos o elemento da raiz para o valor
[code]‘P’[/code]. Fica mais legível se usarmos a função [code]-:[/code]:

Nós podemos ir para cima se quisermos e substituir o elemento com o misterioso [code]'X'[/code]:

Ou se escrevermos com [code]-:[/code]:

Mover para cima é fácil pois as migalhas que deixamos formam uma parte da estrutura de dados que não estamos focando, mas se invertermos, torna-se mais difícil. É por isso que quando queremos mover para cima nós não temos que começar da raiz e ir fazendo nossa descida, mas nós só pegamos o topo de uma árvore invertida, assim, deixando de ser invertida e adicionando uma parte ao foco.

Cada nó tem duas sub-árvores, mesmo que essas sub-árvores sejam vazias. Então, se nós focarmos em um sub-árvore vazia, uma coisa que podemos fazer é substituir ela por uma sub-árvore não vazia inserindo uma árvore a um nó folha. O código para isso é simples:

Nós recebemos uma árvore e um zipper e retornamos um novo zipper que tem o foco mudado para a árvore fornecida. Não só podemos estender árvores desse modo nós podemos substituir sub-árvores vazias por novas árvores e substituir uma sub-árvore por completo. Vamos adicionar uma árvore ao lado esquerda da nossa [code]freeTree[/code]:

[code]newFocus[/code] é focada agora na árvore que acabamos de adicionar e o resto permanece contido nas migalhas. Se nós usarmos a função [code]goUp[/code] para caminhar até o topo da árvore, irá ser a mesma árvore [code]freeTree[/code] porém com um elemento [code]‘Z’[/code] adicionado a sua esquerda.

- Eu vou direto para o topo, oh yeah, até onde o ar é fresco e limpo!

Fazer uma função que percorre todo caminho até o topo da árvore, independente de onde está o foco, é bem fácil. Aqui está:

Se nossa trilha de migalhas reforçadas estiver vazia siginifica que nós já estamos na raiz da árvore, então nós só voltamos o foco. Caso contrário, nós vamos até o topo até conseguir o foco do pai e aplicamos recursivamente a função [code]topMost[/code] para isso. Então agora nós podemos percorrer toda a árvore usando as funções [code]modify[/code] e [code]attach[/code] à medida que avançamos e, em seguida, quando terminarmos de fazer as nossas modificações, utilizamos [code]topMost[/code] para focar na raiz da árvore e ver as modificações na pespectiva certa.