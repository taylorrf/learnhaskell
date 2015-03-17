Focando nas listas
==================

Zippers também podem ser usados como qualquer outra estrutura de dados, o que não é nenhuma surpresa 
já que eles podem ser usados para focar em sub-listas de listas. Afinal de contas, listas são bastante 
parecidas com árvores, apenas quando um nó em uma árvore tem um elemento (ou não) e várias sub-árvore,
um nó em uma lista tem um elemento e apenas uma única sub-lista. Quando nós <a href="making-our-own-types-and-typeclasses#recursive-data-structures">implementamos a nossa própria lista</a>, 
definimos nosso tipo de dados da seguinte forma:

Compare isso com a nossa definição de árvore binária e será fácil ver como listas podem ser vistas 
árvores quando cada nó tem somente uma única sub-árvore.

Uma lista como [code][1,2,3][/code] pode ser escrita como [code]1:2:3:[][/code]. Contendo a cabeça 
da lista, que será [code]1[/code] e então a cauda da lista, que será [code]2:3:[][/code].
Por sua vez, [code]2:3:[][/code] também tem a sua cabeça, que é [code]2[/code] e uma cauda, que é 
[code]3:[][/code]. Com [code]3:[][/code], o [code]3[/code] é a cabeça e a cauda é a lista vazia [code][][/code].

Vamos fazer um zipper para listas. Para mudar o nosso foco de sub-listas em listas, nós nos movemos
para frente ou para trás (enquanto que em árvores nos movemos para esquerda ou para direita). A parte
focada será uma sub-árvore e juntos com isso nós vamos deixar migalhas de pão à medida que avançamos.
Agora, em que será que consiste uma migalha de pão para uma lista? Quando estamos lidando com árvores 
binária, dizemos que a migalha de pão terá que segurar o elemento na raiz do nó pai, juntamente com 
todas as sub-árvores que não escolhemos. Ele também terá que lembrar se fomos para esquerda ou para
direita. Então, ele terá que ter todas as informações que um nó tem exceto para a sub-árvore que escolheu para se 
concentrar.

Listas são mais simples que árvores, então não precisamos lembrar se fomos para esquerda ou direita, 
isso porque existe somente uma forma de ir mais fundo em listas. Como há somente uma única sub-árvore 
em cada nó, não precisamos lembrar do caminho que fizemos para chegar lá. E ao que tudo indica, tudo
o que precisamos lembrar é do elemento anterior. Se temos uma lista como [code][3,4,5][/code] e 
sabemos que o elemento anterior é [code]2[/code], então nós podemos voltar apenas inserindo aquele 
elemento na cabeça de nossa lista, obtendo assim [code][2,3,4,5][/code].

Como uma migalha de pão aqui é apenas um elemento, não precisamos necessariamente colocar ela
dentro de um tipo de dados, da forma como fizemos antes com o tipo de dados [code]Crumb[/code] para a árvore
de zippers:

A primeira lista representa a lista em que estamos focando e a segunda lista representa a migalha de pão.
Vamos criar uma função que vai pra frente e para trás dentro das listas:

Quando estamos indo para frente, nós focamos na cauda da lista atual e deixamos a cabeça do elemento
como uma migalha de pão. Quando nos movemos para trás, nós pegamos a última migalha de pão e colocamos
ela no inicio da lista.

Aqui esta as duas funções em ação:

Percebemos que as migalha de pão no caso de listas são nada mais do que apenas a parte reversa da nossa
lista. O elemento que nos afastamos sempre vai para a cabeça da migalha de pão, por isso que é fácil
se mover para trás apenas pegando elemento da cabeça da nossa migalha e usando isso como nosso foco.

Isso também torna mais fácil ver por que chamamos isso de zíper, porque isso realmente se parece
com um ziper que se move para cima e para baixo.

Se você estiver fazendo um editor de texto, você pode usar uma lista de strings para representar 
as linhas de textos que estão atualmente abertas e você pode então usar o zipper para saber 
em qual linha o cursor esta atualmente focado. Ao usar um zipper, pode ser mais fácil também inserir
uma linha em qualquer lugar do texto ou deletar alguma.
