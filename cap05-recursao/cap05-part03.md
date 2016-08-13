Um pouco mais de funções recursivas
===================================

Agora que já sabemos como pensar recursivamente, vamos implementar algumas funções usando recursão. 
Primeiramente, vamos implementar [code]replicate[/code]. [code]replicate[/code] recebe um [code]Int[/code] 
junto de algum elemento e retorna uma lista com várias repetições do mesmo elemento. Por exemplo 
[code]replicate 3 5[/code] retorna [code][5,5,5][/code]. Pensemos sobre a condição limite. Meu palpite 
é de que a condição limite seja 0 ou menos. Se tentarmos replicar alguma coisa zero vezes, devemos 
retornar uma lista vazia. O mesmo para números negativos, porque isso não faria sentido.

Aqui usamos guards em vez de patterns porque estamos interessados em uma condição booleana. Se 
[code]n[/code] é menor ou igual a 0, retorna uma lista vazia. Se não, retorna uma lista que tem 
[code]x[/code] como primeiro elemento e então [code]x[/code] replicado n-1 vezes como a cauda 
(se tratando de listas e algoritmos, a nomenclatura cabeça e cauda é bastante usada, acostume-se). 
Eventualmente, a parte [code](n-1)[/code] fará com que a função chegue à nossa condição limite.

<em>Nota:</em> [code]Num[/code] não é uma subclass de [code]Ord[/code]. Isso significa que o que 
constitui um número não necessariamente precisa aderir a uma ordenação. É por isso que temos que 
especificar ambas as restrições de classe [code]Num[/code] e [code]Ord[/code] quando fazemos adição 
ou subtração e também comparação.

Em seguida, vamos implementar [code]take[/code], que retorna um certo número de elementos de uma lista. 
Por exemplo, [code]take 3 [5,4,3,2,1][/code] retornando [code][5,4,3][/code]. Se tentarmos pegar 0 ou 
menos elementos de uma lista, receberemos uma lista vazia. Também, se tentamos pegar algo de uma lista 
vazia, receberemos uma lista vazia. Note que essas são as duas condições limite. Então, vamos implementar 
a função:

O primeiro pattern define que se tentarmos pegar 0 ou um número negativo de elementos, vamos receber 
a lista vazia. Note que estamos usando [code]_[/code] para comparar com a lista porque não nos 
interessamos pelo seu valor nesse caso. Note também que nós usamos um guard, mas sem o 
[code]otherwise[/code]. Isso significa que se [code]n[/code] for maior do que 0, o pattern matching irá 
para o próximo pattern. O segundo pattern indica que, se tentarmos pegar alguma coisa de uma lista vazia, 
vamos receber uma lista vazia. O terceiro pattern quebra a lista em uma cabeça e uma cauda. E então nós 
dizemos que pegar [code]n[/code] elementos de uma lista é igual a uma lista que tem [code]x[/code] 
como a cabeça e então uma outra lista que pega [code]n-1[/code] elementos da cauda como cauda. Tente usar 
um pedaço de papel para escrever como o algoritmo funcionaria em, digamos, 3 de [code][4,3,2,1][/code].

[code]reverse[/code] simplesmente inverte uma lista. Pense sobre a condição limite. O que ela seria? 
Vamos lá... é a lista vazia! Uma lista vazia invertida é igual à própria lista vazia. Legal. E o resto? 
Bem, você poderia dizer que se dividirmos uma lista em cabeça e cauda, a lista invertida é igual à 
cauda invertida com a cabeça no final.

E aí está pronto.

Como Haskell aceita listas infinitas, nossa recursão não necessariamente precisa ter uma condição 
limite. Mas se não tiver, nós ou ficaremos repetindo algo infinitamente ou vamos produzir uma estrutura 
de dados infinita, como uma lista infinita. O lado bom das listas infinitas é que nós podemos 
cortá-las onde quisermos. [code]repeat[/code] pega um elemento e retorna uma lista infinita que tem 
apenas aquele elemento. Uma implementação recursiva disso é realmente fácil. Olhe:

Chamar [code]repeat 3[/code] nos dará uma lista que começa com [code]3[/code] e então tem uma 
quantidade infinita de 3 como cauda. Então chamar [code]repeat 3[/code] resuta em algo como 
[code]3:repeat 3[/code], que é [code]3:(3:repeat 3)[/code], que é [code]3:(3:(3:repeat 3))[/code]. 
[code]repeat 3[/code] nunca terminará o pattern matching, enquanto [code]take 5 (repeat 3)[/code] 
nos retornará uma lista de cinco 3's. Então, essencialmente, é o mesmo que fazer 
[code]replicate 5 3[/code].

[code]zip[/code] pega duas listas e mescla elas. [code]zip [1,2,3] [2,3][/code] retorna 
[code][(1,2),(2,3)][/code], porque ela trunca a lista maior para combinar com o tamanho da lista menor. 
E se a gente chamar <i>zip</i> passando uma lista vazia? Bem, vamos receber então, uma lista vazia. 
Essa então será a nossa condição limite. Contudo, [code]zip[/code] pega duas listas como parâmetro, 
e assim haverá duas condições limite.

Os primeiros dois patterns dizem que se a primeira ou segunda lista é vazia, nós pegamos uma lista 
vazia. O terceiro diz que a chamada de zip em duas listas é igual a parear as suas cabeças e então 
continuar a chamar zip nas caudas. A chamada de zip nas listas [code][1,2,3][/code] e 
[code]['a','b'][/code] eventualmente tentará chamar zip em [code][3][/code] e [code][][/code]. 
Chega-se então ao padrão da condição limite e assim o resultado é [code](1,'a'):(2,'b'):[][/code], 
que é exatamente o mesmo que [code][(1,'a'),(2,'b')][/code].

Vamos implementar mais uma função da biblioteca padrão &mdash; [code]elem[/code]. Ela pega um 
elemento e uma lista e vê se aquele elemento está na lista. A condição limite, como na maioria das 
vezes com listas, é a lista vazia. Nós sabemos que uma lista vazia não contém elemento algum, então 
temos certeza que ela não tem o que estamos procurando.

Bastante simples e previsível. Se a cabeça não é o elemento, então nós checamos a cauda. Se alcançarmos 
uma lista vazia, então o resultado é [code]False[/code].

