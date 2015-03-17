Ande na linha
=============

Agora que sabemos como alimentar um valor [code]Maybe a[/code] para uma função do tipo 
[code]a -&gt; Maybe b[/code], tendo em vista o contexto de uma possível falha, vamos ver como 
podemos usar [code]&gt;&gt;=[/code] várias vezes para lidar com cálculos de vários valores de 
[code]Maybe a[/code].

Pierre decidiu fazer uma pausa em seu trabalho na fazenda de peixes e tentar caminhar em uma corda banda. 
Isso não é ruim para ele, mas ele tem um problema: pássaros mantem seu pouso em ponto de equilíbrio! 
Eles vêm e descansam um pouco, batem um papo com seus amigos pássaros e depois decolam a procura de 
migalhas de pão. Isso não incomodaria tanto ele se o número de aves no lado esquerdo do bastão fosse 
sempre igual ao número de aves no lado direito. Mas às vezes, todos os pássaros decidem que eles gostam 
de um lado e assim ele desequilibra-se, o resultado é uma queda embaraçosa para Pierre (ele está usando 
uma rede de segurança).

Digamos que ele mantém o seu equilíbrio, se a diferença do número de pássaros do lado esquerdo do bastão 
em relação ao número de pássaros do lado direito seja três. Assim, se tem um passado do lado 
direito e quatro pássaros do lado esquerdo, ele está em equilibrio, Mas se um quinto pássaro pousa no 
lado esquerdo, ele perde o equilíbrio e leva um tombo.

Vamos simular pássaros pousando e voando longe do bastão e ver se Pierre ainda está nele depois de 
um determinado número de pássaros chegando e partindo. Por exemplo, queremos ver o que acontece com 
Pierre se o primeiro pássaro chega no lado esquerdo, em seguida, quatro pássaros ocupam o lado direito 
e depois o pássaro que estava no lado esquerdo decide voar para longe.

Podemos representar o bastão com um simples par de números inteiros. O primeiro componente irá significar 
o número de aves no lado esquerdo e o segundo componente o número de aves no lado direito:

Primeiro criamos um tipo sinônimo para [code]Int[/code], chamados de [code]Birds[/code], porque estamos 
usando números inteiros para representar quantos pássaros existem. Em seguida criamos um tipo sinônimo 
de [code](Birds,Birds)[/code] e chamamos ele de [code]Pole[/code] (para não ser confundido com uma 
pessoa de ascendência polonesa).

Em seguida, que tal criar uma função que recebe um número de pássaros e pousa eles em um lado do 
bastão. Aqui estão as funções:

Coisa muito simples. Vamos testa-los:

Para fazer os pássaros voarem para longe nós apenas recebemos um número negativo de pássaros pousando 
em um lado. Porque pousar um pássaro em [code]Pole[/code] retona um [code]Pole[/code], podemos 
encadear a aplicação de [code]landLeft[/code] e [code]landRight[/code]:

Quando aplicamos a função [code]landLeft 1[/code] para [code](0,0)[/code] temos [code](1,0)[/code]. 
Então, pousamos um pássaro no lado direito, resultando em [code](1,1)[/code]. Finalmente dois 
pássaros pousam no lado esquerdo, resultando em [code](3,1)[/code]. Nós aplicamos uma função em algo 
escrevendo primeiro a função e depois escrevendo os parâmetros, mas aqui será melhor se o bastão vier 
primeiro e depois a função de pouso. Se fizermos uma função assim:

Podemos aplicar funções escrevendo primeiro os parâmetros e depois a função:

Ao usar isso, podemos repetidamente pousar pássaros no bastão de um modo mais legível:

Muito legal! Este exemplo é equivalente ao anterior, onde repetidamente pousamos os pássaros no bastão, 
porém ele é mais legível. Aqui, está mais obvio que começamos com [code](0,0)[/code] e depois pousamos um 
pássaro na esquerda, depois outro na direita e finalmente dois na esquerda.

Até agora está bom, mas o que acontece se 10 pássaros pousam em um lado?

10 pássaros no lado esquerdo e somente 3 no lado direito? Isso com certeza vai fazer o pobre Pierre 
cair no ar! Isso é bastante obvio aqui, mas o que acontece se tivermos uma sequência de pousos assim:

Podemos pensar que está tudo certo, mas se você seguiu os passos até aqui, vai vai notar que no momento 
existem 4 pássaros no lado direito e nenhum pássaro no lado esquerdo! Para corrigir isso, precisamos dar
mais uma olhada em nossas funções [code]landLeft[/code] e [code]landRight[/code]. Pelo que percebemos, 
queremos que essas funções possam falhar. Ou seja, queremos que elas retornem um novo bastão se o 
equilíbrio estiver certo, mas que falhem se os pássaros pousarem de forma desequilibrada. E qual a melhor
maneira de adicionar um contexto de falha a um valor do que usar [code]Maybe[/code]! Vamos refazer 
essa função:

Em vez de retornar um [code]Pole[/code] esta função agora retorna um [code]Maybe Pole[/code]. Ela ainda 
tem o número de pássaros do antigo bastão como antes, mas em seguida ela verifica se o pouso de muitos 
pássaros no bastão deixaria Pierre em desequilíbrio. Usamos guardas para verificar se a diferença 
entre o número de pássaros e o novo bastão é menor do que 4. Se for, evolvemos o novo bastão em um 
[code]Just[/code] e retornamos ele. Se não for, retornamos um [code]Nothing[/code], indicando uma falha.

Vamos ver esses bebês de uma vez:

Legal! Quando pousamos pássaros sem deixar Pierre em desequilíbrio, recebemos um novo bastão envolvido 
em um [code]Just[/code]. Mas quando vários pássaros ficaram em um só lado do bastão, recebemos um 
[code]Nothing[/code]. Isso é legal, mas parece que perdemos a habilidade de repetidamente pousar 
pássaros no bastão. Nós não podemos mais fazer [code]landLeft 1 (landRight 1 (0,0))[/code] porque quando 
aplicamos [code]landRight 1[/code] para [code](0,0)[/code], nós não recebemos mais um [code]Pole[/code], 
mas um [code]Maybe Pole[/code]. [code]landLeft 1[/code] pega um [code]Pole[/code] e não um 
[code]Maybe Pole[/code].

Precisamos de uma maneira de ter um [code]Maybe Pole[/code] e alimentar isso para uma função que 
leva um [code]Pole[/code] e retorna um [code]Maybe Pole[/code]. Felizmente, temos 
[code]&gt;&gt;=[/code], que faz exatamente isso para [code]Maybe[/code]. Vamos dar uma olhada:

Lembre-se, [code]landLeft 2[/code] tem um tipo de [code]Pole -&gt; Maybe Pole[/code]. Não 
poderíamos simplesmente alimentar este [code]Maybe Pole[/code] que é o resultado de 
[code]landRight 1 (0,0)[/code], assim nós usamos [code]&gt;&gt;=[/code] para receber esse valor com um 
contexto e devolvê-lo a [code]landLeft 2[/code]. [code]&gt;&gt;=[/code] de fato nos permite tratar o valor de 
[code]Maybe[/code] como um valor com um  contexto porque se jogarmos um [code]Nothing[/code] para 
dentro de [code]landLeft 2[/code], o resultado será [code]Nothing[/code] e uma falha será propagada:

Com isso, podemos agora encadear pousos que podem falhar porque [code]&gt;&gt;=[/code] nos 
permite alimentar um valor monadico para uma função que leva um valor normal.

Aqui está a sequência dos pássaros pousando:

No início, usamos [code]return[/code] para pegar um bastão e envolver ele em um [code]Just[/code]. 
Poderíamos simplesmente ter aplicado [code]landRight 2[/code] para [code](0,0)[/code], isso seria o 
mesmo, mas desta forma podemos ser mais consistentes utilizando [code]&gt;&gt;=[/code] para todas as 
funções. [code]Just (0,0)[/code] é alimentado para [code]landRight 2[/code], resultando em 
[code]Just (0,2)[/code]. Que, por sua vez, é alimentado para [code]landLeft 2[/code], resultando 
em [code]Just (2,2)[/code], e assim por diante.

Lembre-se, no exemplo de antes introduzimos falha dentro da rotina de Pierre:

Isso não simula muito bem sua interação com os pássaros, porque no meio há um desequilíbrio, mas 
o resultado não reflete isso. Mas vamos ver agora como usamos aplicações monadic 
([code]&gt;&gt;=[/code]) ao invés de aplicações normais:

Incrível. O resultado final representa uma falha, que é exatamente o que esperávamos. Vamos ver como 
esse resultado foi obtido. Primeiro, [code]return[/code] envia [code](0,0)[/code]para o contexto 
padrão, tornando isso um [code]Just (0,0)[/code]. Em seguida, ocorre o 
[code]Just (0,0) &gt;&gt;= landLeft 1[/code] . Uma vez que o [code]Just (0,0)[/code] é um valor 
[code]Just[/code], [code]landLeft 1[/code] é aplicado para [code](0,0)[/code], resultando em um 
[code]Just (1,0)[/code], porque os pássaros ainda estão relativamente em equilíbrio. Em seguida, 
[code]Just (1,0) &gt;&gt;= landRight 4[/code] toma lugar e resulta em [code]Just (1,4)[/code] com o 
equilíbrio dos pássaros ainda intacto, embora somente um pouco. [code]Just (1,4)[/code] é enviado 
para [code]landLeft (-1)[/code]. Isso significa que [code]landLeft (-1) (1,4)[/code] toma o lugar. Por 
causa da forma como [code]landLeft[/code] funciona, isso resulta em um [code]Nothing[/code], porque o 
bastão resultante esta fora de equilíbrio. Agora que temos um [code]Nothing[/code], ele é enviado para 
[code]landRight (-2)[/code], mas como isso é um [code]Nothing[/code], o resultado é automaticamente 
[code]Nothing[/code], já que não temos nada para aplicar em [code]landRight (-2)[/code].

Nós não poderíamos ter conseguido isso apenas usando [code]Maybe[/code] como um <i>applicative</i>. 
Se você tentar isso, você vai ficar preso, porque <i>applicative functors</i> não permitem que os 
<i>applicative values</i> interajam muito bem uns com os outros. Eles podem, quando muito, ser usados 
como parâmetros em uma função usando o estilo <i>applicative</i>. Os operadores de aplicativos vão 
buscar esses resultados a alimentá-los para a função de maneira apropriada para cada aplicativo, 
e depois, colocar o valor do aplicativo final junto, mas não são muito interativos entre si. Aqui, 
porém, cada passo é baseado no resultado anterior. Em cada pouso, o possível resultado anterior é 
examinado e o bastão é marcado como equilibrado. Isso determina se o pouso vai ter sucesso ou falha.

Nós podemos também inventar uma função que ignora o número atual de pássaros em equilíbrio no bastão 
apenas fazendo Pierre desequilibrar e cair. Podemos chamar isso de [code]banana[/code]:

Agora podemos encadear isso junto com nossos pássaros pousando. Ele sempre fará nosso caminho falhar, 
porque ignora o que esta recebendo e sempre retorna uma falha, Confira:

O valor [code]Just (1,0)[/code] é alimentado para [code]banana[/code], mas produz um 
[code]Nothing[/code], que faz tudo resultar em um [code]Nothing[/code]. Que infeliz!

Em vez de criar funções que ignoram essas entradas e apenas retornam um valor monadic predeterminado, 
podemos usar a função [code]&gt;&gt;[/code], cuja a implementação padrão é:

Normalmente, ao passar algum valor para uma função que ignora seus parâmetros e sempre retorna um 
predeterminado valor sempre resultará nesse predeterminado valor. No entanto, com monads 
o contexto e significado dele devem ser considerados também. Aqui esta como [code]&gt;&gt;[/code] 
funciona com [code]Maybe[/code]:

Se você substituir [code]&gt;&gt;[/code] com [code]&gt;&gt;= \_ -&gt;[/code], é fácil ver por que 
ele age e como ele faz.

Podemos substituir nossa função [code]banana[/code] no encadeamento com um [code]&gt;&gt;[/code] e, em 
seguida, com um [code]Nothing[/code]:

E então vamos, garantida e obviamente falhar!

Também vale a pena dar uma olhada em como seria se não tivéssemos feito a escolha inteligente 
de tratar os valores [code]Maybe[/code] como valores com um contexto de falha e alimenta-los em 
funções como fizemos. Aqui esta como uma serie de pássaros pousando deveria parecer:


Pousamos um passáro no lado esquerdo e então examinamos a possibilidade de falha e a possibilidade
de sucesso. Em caso de falha, retornamos um [code]Nothing[/code]. Em caso de sucesso, pousamos os 
pássaros do lado direito e depois fazemos a mesma coisa novamente. Convertendo essa monstruosidade 
em um encadeamento organizado de aplicações monadic com [code]&gt;&gt;=[/code] é um clássico exemplo de 
como a monad [code]Maybe[/code] nos poupa tempo quando temos que fazer operações sucessivas que são 
baseadas nas que talvez falharam.

Note como a implementação de [code]&gt;&gt;=[/code] por [code]Maybe[/code] faz exatamente essa 
lógica de ver se o valor é [code]Nothing[/code] e se for isso , retorna imediatamente um 
[code]Nothing[/code] e se não for, segue adiante com o que esta dentro de [code]Just[/code].

Nessa seção, abordamos algumas funções e vimos como elas poderiam funcionar melhor se os valores que 
elas retornam suportassem falhas. Transformamos esses valores em valores [code]Maybe[/code] e 
substituímos <i>aplications functor</i> normais com [code]&gt;&gt;=[/code], ganhamos de graça um 
mecanismo para tratar falhas, porque [code]&gt;&gt;=[/code] é usado para preservar o contexto de um valor no qual 
se esta aplicando às funções. Neste caso, o contexto era que nossos valores eram valores com falha e 
assim quando aplicavamos funções para esses valores, a possibilidade de falhar sempre foi levada em conta.
