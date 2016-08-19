Mapas e filtros
===============

[fuction]map[/function] recebe uma função e uma lista e aplica esta função em todos elementos da 
lista, produzindo uma nova lista. Vamos ver como é a sua declaração de tipo e como ele é definido.

A declaração de tipo nos diz que esta função recebe um [code]a[/code] e retorna um [code]b[/code], 
uma lista de [code]a[/code]'s e retorna uma lista de [code]b[/code]'s. O interessante aqui é dar uma 
olhada na assinatura de tipos da função, algumas vezes só olhando isso você já consegue saber como ela funciona. 
[code]map[/code] é realmente uma das mais versáteis funções de ordem superior que poderemos utilizar em milhares de 
formas diferentes. Veja ela em ação:

Você provavelmente já sabe que cada um deles pode ser realizado com uma compreensão de lista. 
[code]map (+3) [1,5,3,1,6][/code] é o mesmo que escrever [code][x+3 | x &lt;- [1,5,3,1,6]][/code]. 
Tanto faz, utilizar [code]map[/code] é muito mais legível em muitas situações onde você só pode 
aplicar alguma função em um elemento da lista, especialmente depois que você estiver lidando com 
mapas de mapas ter que lidar com uma série de colchetes pode ser um pouco bagunçado.

[fuction]filter[/code] é uma função que recebe um predicado (um predicado é uma função que diz que 
qualquer coisa é verdadeira ou não, nesse caso, uma função que retorna um valor booleano) e uma 
lista e então retorna uma lista com elementos que satisfazem o predicado. A declaração de tipo e a 
implementação é algo como:

Bastante simples. Se [code]p x[/code] se transformar em [code]True[/code], o elemento será incluido 
em uma nova lista. Se isto não acontecer, ficará de fora. Alguns exemplos úteis:

Tudo isto pode ser obtido com uma compreensão de lista e pelo uso de predicados. Não há um conjunto 
de regras para quando devemos utilizar [code]map[/code] e [code]filter[/code] ao invés de compreensão 
de lista, você só deve decidir qual será o mais légível dependendo do código e do contexto. 
O [code]filter[/code] equivale a aplicar diversos predicados em uma compreensão de lista e equivalente 
a filtrar algo diversas vezes ou juntar o predicado coerentemente com a função [code]&amp;&amp;[/code].

Lembra da função quicksort do <a href="recursion">capítulo anterior</a>? Utilizamos compreensão de 
lista para filtrar os elementos que forem menores do que (ou igual a) e maior do que o pivô. Podemos obter 
a mesma funcionalidade de um jeito mais legível com o uso do [code]filter[/code]:

Mapeamento e filtragem é o pão e a manteiga de toda a caixa de ferramenta da programação funcional. 
Não importa se fizermos isso com [code]map[/code] e [code]filter[/code] funções ou compreensão de lista. 
Lembre-se como resolvemos o problema para achar o triângulo retângulo com uma determinada circunferência. 
Com programação imperativa, nós deveriamos resolvê-lo aninhando três loops e então testando se a 
combinação atual satisfaz o triângulo retângulo e se tem o perímetro certo. Se fosse este o caso, 
teríamos que mostrá-lo na tela ou algo assim. Na programação funcional, os padrões são obtidos 
com mapeamentos e filtragens. Você faz uma função que recebe um valor e produz algum resultado. 
Nós mapeamos esta função mais uma lista de valores e então nós filtramos o resultado da lista para 
que os resultados satisfaçam a nossa busca. Graças à preguiça de Haskell, sempre que você mapear algo 
sobre uma lista várias vezes e filtrá-la várias vezes, ele só irá passar pela lista uma vez.

Vamos <em>encontrar o maior número em 100.000 que é divisível por 3829</em>. Para fazer isso, 
vamos filtrar um conjunto de possibilidades em que nós sabemos que a solução consiste.

Primeiro vamos fazer uma lista com todos os números menores do que 100.000, em ordem decrescente. 
Então vamos filtrá-lo pelo seu predicado e como os números são ordenados de forma decrescente, o maior 
número que irá satisfazer nosso predicado será o primeiro elemento filtrado da lista. Não precisamos 
utilizar uma lista infinita para o nosso conjunto inicial. Eis a preguiça em ação novamente. Como nós 
acabamos utilizando a <i>cabeça</i> da lista filtrada, não importa se a lista filtrada for finita ou 
infinita. A análise para quando a primeira solução adequada for encontrada.

E agora, nós queremos <em>obter a soma de todos os quadrados ímpares que sejam menores do que 10.000</em>. 
Primeiramente, como iremos usá-lo em nossa solução, irei introduzir aqui a função 
[function]takeWhile[/function]. Ela recebe um predicado e uma lista e então vai para o inicio da lista 
e retorna os elementos que satisfazem o predicado. Se o elemento encontrado por ele não satisfazer o 
predicado, ele para. Se você quer a primeira palavra da string 
[code]"elefantes é que sabem fazer festa"[/code], então faça [code]takeWhile (/=' ') "elefantes é que 
sabem fazer festa"[/code] e ele lhe retornará [code]"elefantes"[/code]. Beleza. A soma de todos os 
quadrados ímpares que são menores do que 10.000. Primeiramente, vamos começar mapeando a função 
[code](^2)[/code] em uma lista infinita [code][1..]][/code]. Então filtramos somente os ímpares. 
E agora, vamos pegar os elementos a partir desta lista que tem todos os menores do 10.000. Finalmente, 
vamos ter a soma de toda essa lista. Nem sempre precisamos definir uma função para isso, vamos fazer 
direto em uma linha no GHCI:

Demais! Começamos com alguns dados iniciais (a lista infinita de todos os números naturais) e então 
nós mapeamos isto, filtramos e cortamos até que fosse o que precisávamos e depois nós somamos. Também 
podemos escrever isto utilizando compreensão de lista: 

É uma questão de gosto, escolha a forma que você achar mais bonita. Novamente, a magnífica preguiça do 
Haskell torna isto possível. Nós podemos mapear e filtrar uma lista infinita, justamente porque ele 
não mapeia e filtra do jeito convencional, senão essa tarefa seria bem demorada. Somente quando forçamos 
Haskell a mostrar para nós a soma realizada na função [code]sum[/code] é que ele diz para o 
[code]takeWhile[/code] que ele precisa daqueles números. [code]takeWhile[/code] força que o 
mapeamento e a filtragem ocorra, porém somente se algum número maior ou igual do que 10.000 for 
encontrado.

Nosso próximo problema, será lidar com a <a href="http://pt.wikipedia.org/wiki/Conjectura_de_Collatz" 
target="_blank">sequência de Collatz</a>. Nós pegamos um número. Se ele for par, dividimos por dois. 
Se for ímpar, dividimos ele por 3 e somamos com mais 1. Pegamos o número resultante e aplicamos a mesma 
coisa nele, produzindo um novo número e assim por diante. Na essência, nós temos uma sequência de 
números. Isto é feito para todos os números iniciais, a sequência termina no número 1. Então se 
tivermos inicialmente o número 13, teremos esta sequência: <i>13, 40, 20, 10, 5, 16, 8, 4, 2, 1</i>. 
13*3 + 1 igual a 40. 40 dividido por 2 é 20, etc. Como vimos a sequência tem 10 termos.

Agora o que nós queremos saber é: <em>para todos os números iniciais entre 1 e 100, quantas sequências 
tem com um tamanho maior do que 15?</em> Primeiramente, vamos escrever uma função para obter uma 
sequência:

Como a sequência termina em 1, esse seria o caso de limite. Essa é uma função recursiva bem padrão.

Feito! Esta funcionando corretamente. E agora, a função para nos dizer a resposta para a nossa questão:

Mapeamos a função [code]chain[/code] para [code][1..100][/code] para obter uma lista das sequências, com 
eles próprios representando-se como listas. Então os filtramos com o predicado só para verificar se 
aquelas listas tem o tamanho maior do que 15. Novamente nós filtramos, e conseguimos ver quantas 
sequências são deixadas na lista resultante.

<em>Nota:</em> Esta função tem o tipo [code]numLongChains :: Int[/code] porque [code]length[/code] 
retorna um [code]Int[/code] ao invés de um [code]Num a[/code] por razões históricas. Se quisermos 
retornar um [code]Num a[/code] mais geral, nós deveremos usar o [code]fromIntegral[/code] no length 
resultante.

Usando [code]map[/code], conseguimos fazer também algumas coisas como [code]map (*) [0..][/code], 
se não por algum motivo que seja então para ilustrar como currying funciona e como funções (parcialmente 
aplicadas) são valores reais que você pode passar para outra função ou colocar dentro de listas (você 
só não pode transforma-las em strings). Por enquanto, apenas mapeamos funções que pegam somente um 
parâmetro sobre uma lista, como [code]map (*2) [0..][/code] que resulta em uma lista do tipo 
[code](Num a) =&gt; [a][/code], porém podemos fazer também [code]map (*) [0..][/code] sem problemas.
O que acontece aqui é que aquele número na lista é aplicado para a função [code]*[/code], que tem o 
tipo [code](Num a) =&gt; a -&gt; a -&gt; a[/code]. Aplicar somente um parâmetro para um função que 
recebe dois parâmetros, retorna uma função que pega um parâmetro. Se a gente mapear [code]*[/code] sobre 
a lista [code][0..][/code], vamos ter de volta uma função que pega somente um parâmetro, sendo então 
[code](Num a) =&gt; [a -&gt; a][/code]. [code]map (*) [0..][/code] produz uma lista igual a que obteriamos
se escrevessemos [code][(0*),(1*),(2*),(3*),(4*),(5*)..[/code].

Receber o elemento com índice [code]4[/code] a partir da nossa lista nos retorna uma função equivalente a 
[code](4*)[/code]. Então, apenas aplicamos [code]5[/code] na função. Que é o mesmo que escrever 
[code](4*) 5[/code] ou somente [code]4 * 5[/code].
