Uma introdução às listas
========================

Assim como as listas de compras no mundo real, listas em Haskell são extremamente úteis. Esta é a estrutura de dados mais utilizada e pode ser utilizada em muitas e em diversas formas para modelar e resolver uma série de problemas. Listas são MUITO legais. Neste capitulo veremos o básico de listas, strings (que são listas) e compreensões de listas. 

Em Haskell, listas são estruturas de dados homogêneas. Ela armazena vários elementos do mesmo tipo. Isto quer dizer que podemos ter uma lista com inteiros ou uma lista de caracteres, porém não podemos ter uma lista com inteiros e alguns caracteres. E agora, uma lista! 

Nota: Podemos utilizar a palavra-chave [code]let[/code] para definir o nome correto no GHCi. Fazer [code]let a = 1[/code] dentro do GHCi é o equivalente a escrever [code]a = 1[/code] em um script e carregá-lo. 

Como você pode ver, listas são caracterizadas por utilizar colchetes e os valores nas listas são separados por vírgulas. Se tentarmos uma lista como [code][1,2,'a',3,'b','c',4][/code], o Haskell irá reclamar que os caracteres (que são, alias, demarcados entre aspas simples) não são números. Falando em caracteres, strings são simplesmente listas de caracteres. [code]"hello"[/code] é só um atalho para [code]['h','e','l','l','o'][/code]. Como strings são listas, podemos utilizar funções de listas nelas, o que é realmente útil.

Uma prática comum é colocar duas listas juntas. Fazemos isso utilizando o operador [code]++[/code].

Veremos repetidamente o uso do operador [code]++[/code] em strings grandes. Quando juntamos duas listas (mesmo se você adicionar uma lista de um elemento só em outra lista, como: [code][1,2,3] ++ [4][/code]), internamente o Haskell irá percorrer toda a lista que esta do lado esquerdo do [code]++[/code]. Isto não é um problema quando não estamos lidando com listas muito grandes. Porém colocar algo no final de uma lista com cinquenta milhões de elementos irá demorar um pouco. No entanto, colocar alguma coisa no início de uma lista utilizando o operador [code]:[/code] (também chamado de <i>contra</i> operador) será instantâneo.

Observe que o [code]:[/code] recebe um número e uma lista de números ou um caractere e uma lista de caracteres, enquanto o [code]++[/code] recebe duas listas. Mesmo que você esteja adicionando um elemento ao final de uma lista com [code]++[/code], você terá que demarcá-la com colchetes para que se torne lista.

[code][1,2,3][/code] é na verdade um atalho para [code]1:2:3:[][/code]. [code][][/code] é uma lista vazia. Se acrescentarmos [code]3[/code] nele, ele irá se tornar [code][3][/code]. Se acrescentarmos [code]2[/code], se tornará [code][2,3][/code] e assim por diante.

<em>Nota:</em> [code][][/code], [code][[]][/code] e [code][[],[],[]][/code] são coisas diferentes. O primeiro é uma lista vazia, o segundo é uma lista que contém uma lista vazia, o terceiro é uma lista que contém três listas vazias.

Se quiser obter um elemento de uma lista pelo seu índice, utilize [code]!![/code]. O índice inicia a partir de 0.

Mas se tentar obter o sexto elemento de uma lista que contém somente quatro elementos, você obterá um erro portanto seja cuidadoso!

Listas também podem conter listas. Elas também podem conter listas que contêm listas que contêm listas...

As listas dentro de uma lista podem conter <i>lengths</i> diferentes, mas eles não podem ser de tipos diferentes. Assim como você não pode ter uma lista que tem alguns caracteres e alguns números, você não pode ter uma lista que contém algumas listas de caracteres e algumas listas de números.

As listas poderão ser comparadas se as coisas que elas contêm puderem ser comparadas. Quando utilizamos [code]&lt;[/code], [code]&lt;=[/code], [code]&gt;[/code] e [code]&gt;=[/code] para comparar listas, eles são comparados na <a href="http://pt.wikipedia.org/wiki/Ordem_lexicogr%C3%A1fica" target="_blank">ordem lexicográfica</a>. Primeiro os cabeçalhos são comparados. Se eles forem iguais então os segundos elementos são comparados, etc.

Podemos fazer mais algumas coisas com listas. Aqui estão algumas funções básicas:

[code]head[/code] recebe uma lista e retorna o seu <i>head</i>. O <i>head</i> (cabeça) de uma lista é basicamente o primeiro elemento.

[code]tail[/code] recebe uma lista e retorna a sua "cauda". Em outras palavras, ele retira o elemento da cabeça da lista sobrando apenas a cauda.

[code]last[/code] recebe uma lista e retorna o seu último elemento.

[code]init[/code] recebe uma lista e retorna tudo com exceção do último elemento.

Se pensarmos em uma lista como um monstro, aqui está o que é o que.

Mas o que acontece se tentarmos obter o <i>head</i> de uma lista vazia?


WOW! Tudo explode bem na nossa cara! Isso não é um mostro, não tem cabeça! Quando for utilizar [code]head[/code], [code]tail[/code], [code]last[/code] e [code]init[/code], tenha o cuidado para não utilizar em listas vazias. Este erro não pode ser capturado em tempo de compilação por isso é sempre bom tomar certos cuidados antes de pedir para o Haskell lhe dar algum elemento de uma lista vazia.

[code]length[/code] recebe uma lista e retorna o seu <i>length</i> (tamanho), obviamente.

[code]null[/code] verifica se a lista é vazia. Se for, então retorna [code]True[/code], senão retorna [code]False[/code]. Utilize esta função no lugar de [code]xs == [][/code] (Caso você tiver uma lista chamada [code]xs[/code])

[code]reverse[/code] reverte uma lista.

[code]take[/code] recebe um número e uma lista. Ele extrai a quantidade de elementos desde o início da lista. Observe.

Observe que se tentarmos tirar mais elementos do que há na lista, ele retorna só a lista. Se tentarmos retornar 0 elementos, receberemos uma lista vazia.

[code]drop[/code] funciona de forma similar, só que retira o número de elementos a partir do ínicio da lista.

[code]maximum[/code] recebe uma lista de coisas que podem ser colocadas em algum tipo de ordem e retorna o seu maior elemento.

[code]minimum[/code] retorna o menor.

[code]sum[/code] recebe uma lista de números e retorna a sua soma.

[code]product[/code] recebe uma lista de números e retorna o seu produto. 

[code]elem[/code] recebe alguma coisa e uma lista de coisas e nos diz se esta coisa é um elemento da lista. Geralmente é chamado como uma função <i>infixa</i> porque é mais fácil de ler dessa maneira.

Estas são algumas funções básicas para operações em listas. Vermos mais funções de listas <a href="modules#data-list">depois</a>.
