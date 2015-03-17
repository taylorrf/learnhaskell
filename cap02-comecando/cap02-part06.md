Tuplas
======

Em alguns casos, tuplas são como listas &mdash; um jeito de armazenar muitos valores em um lugar só. No entanto, existem algumas diferenças fundamentais. Uma lista de números é uma lista de números. Esse é o seu tipo e não importa se tiver um único número nela ou uma quantidade infinita de números. Tuplas, no entanto, são usadas quando você sabe exatamente quantos valores você quer combinar e o seu tipo depende de quantos componentes ela tem e os tipos dos componentes. Tuplas são caracterizadas por parênteses com seus componentes separados por vírgulas.

Outra diferença fundamental é que elas não precisam ser homogêneas. Ao contrário de uma lista, uma tupla pode conter uma combinação de vários tipos.

Pense em como você representaria um vetor bi-dimensional em Haskell. Um jeito seria você usar uma lista. Esse seria um jeito de fazer. E se você quiser colocar alguns vetores em uma lista para representar pontos de uma superfície plana em duas dimensões? Você poderia fazer algo como [code][[1,2],[8,11],[4,5]][/code]. O problema com este método é que podemos fazer algumas coisas como [code][[1,2],[8,11,5],[4,5]][/code], com Haskell não tem problema desde que isso seja uma lista de listas com números, mas meio que não faz sentido. Mas uma tupla de tamanho dois (também chamado de um par) é o seu próprio tipo, sabendo disto aquela lista não pode ter alguns pares e depois umas triplas (uma tupla de tamanho três), mas vamos utilizar mesmo assim. Em vez de contornar os vetores com colchetes, usamos parênteses: [code][(1,2),(8,11),(4,5)][/code]  E se eu quiser uma superfície como [code][(1,2),(8,11,5),(4,5)][/code]? Bem, isto me dará um erro:

Ele esta me dizendo que tentei usar um par e um triplo na mesma lista, isso não pode acontecer. Você não pode fazer uma lista como [code][(1,2),("One",2)][/code] porque o primeiro elemento da lista é um par de números e o segundo elemento é um par que consiste em uma string e um número. Tuplas podem também ser usadas para representar uma variedade de dados. Por exemplo, se eu quiser representar alguns nomes e idades em Haskell, eu poderia utilizar uma tripla: [code]("Christopher", "Walken", 55)[/code]. Como vimos nesse exemplo, tuplas também podem conter listas

Utilize tuplas quando você souber com antecedência que muitos componentes contêm algumas partes de dados já esperadas. Tuplas são muito mais rígidas porque cada diferença de tamanho da tupla é o seu próprio tipo, você não pode escrever uma função geral para anexar elementos para uma tupla - você deve escrever uma função para anexar elementos a um par, uma função para anexar para uma tripla, uma função para uma 4-tuplas, etc.

Enquanto você tiver listas únicas, não existirá tal coisa como uma tupla única. Realmente não faz muito sentido quando você pensa sobre isso. Uma tupla única seria apenas o valor que ele contém e, como tal, não teria nenhum benefício para nós.

Assim como as listas, tuplas podem ser comparadas umas com as outras se seus componentes puderem ser comparados. Você só não pode comparar duas tuplas de tamanho diferente, uma vez que você pode comparar duas listas de tamanhos diferentes. Duas funções úteis que operam em pares:

[function]fst[/code] recebe um par e retorna seu primeiro componente.

[function]snd[/code] recebe um par e retorna seu segundo componente. Surpresa!

Nota: estas funções operam somente em pares. Não funcionam com triplas, 4-tuplas, 5-tuplas, etc. Falaremos sobre a extração de dados de tuplas de diferentes maneiras um pouco mais tarde.

Uma função legal que produz uma lista de pares: [function]zip[/code]. Ela pega duas listas e então as comprimem juntamente em uma única lista juntando os elementos que casarem em pares. Esta é realmente uma função muito simples mas tem uma infinidade de usos. Ela é bastante útil especialmente quando precisamos combinar duas listas em um único formato ou cruzar duas listas simultaneamente. Veja uma demostração:

Ela pareia todos os elementos e produz uma nova lista. O primeiro elemento vai com o primeiro, o segundo com o segundo, etc. Observe que como pares podem ter tipos diferentes entre eles, [code]zip[/code] pode pegar duas listas que contém tipos diferentes e comprimir em uma só. O que acontece se os tamanhos das listas forem diferentes?

A lista maior é simplesmente cortada para combinar com o tamanho da outra menor. Como Haskell é preguiçoso, nós podemos "zippar" listas finitas com listas infinitas:

Aqui esta um problema para combinar tuplas e compreensão de listas: Que triângulo retângulo que tem números inteiros para todos os lados e todos os lados iguais ou menores que 10 e tem um perímetro de 24? Primeiro, vamos tentar gerar todos os triângulos com lados iguais ou menores que 10:

Estamos apenas desenhando a partir de três listas e a nossa função de output irá combiná-los em uma tripla. Se você avaliar isso digitando [code]triangles[/code] no GHCI, você terá uma lista de todos os possíveis triângulos com os lados menores ou iguais a 10. A seguir, iremos adicionar uma condição que todas elas têm de ser triângulos retângulos. Vamos também modificar esta função levando em consideração que o lado b não é maior do que a hipotenusa e esse lado não é maior do que o lado b.

Estamos quase terminando. Agora, acabamos de modificar a função, dizendo que queremos aqueles em que o perímetro é igual a 24.

E cá esta nossa resposta! Este é um padrão comum em programação funcional. Você tem um conjunto inicial de soluções e em seguida você aplica transformações a essas soluções e as filtram até chegar ao resultado correto.