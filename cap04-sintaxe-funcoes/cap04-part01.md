Sintaxe em Funções
==================

** Pattern matching **

Este capítulo irá cobrir algumas construções sintáticas interessantes do Haskell, e começaremos por pattern matching. Pattern matching consiste na pesquisa por padrões em determinados dados e, caso tenha sucesso, fazer algo com ele.
Ao definir funções, você pode definir códigos específicos para cada padrão. Isso gera um código mais conciso, simples e legível. Você pode avaliar qualquer tipo de dado &mdash; números, caracteres, listas, tuplas... Vamos fazer uma função bem simples que verifica se o número dado é sete ou não.

Ao chamar [code]lucky[/code], os padrões serão testados de cima para baixo e, de acordo com o resultado, executado ou não seu corpo. Aqui, o único modo dessa pattern ter sucesso é se ela for 7. Senão, vamos para a segunda, que será executada de qualquer maneira devido ao [code]x[/code]. Essa função ainda poderia ser implementada por uma estrutura <i>if</i>. Mas e se quisermos uma função que diga o número se o parâmetro for de 1 até 5 ou [code]"Não está entre 1 e 5"[/code] para outros? Sem <i>pattern matching</i>, teríamos que escrever uma estrura <i>if then else</i> bem confusa. No entanto, com ele podemos escrever assim:

Veja que se movessemos a última <i>pattern</i> (o <i>else</i> geral) para o topo, sempre diria [code]"Não está entre 1 e 5"[/code], porque seria sempre executado para qualquer número e as linhas seguintes não teriam oportunidade de serem executadas.

Lembra da função fatorial que implementamos anteriormente? Definimos o fatorial de um número [code]n[/code] como sendo [code]product [1..n][/code]. Nós ainda podemos usar recursividade, que é como geralmente é definida na matemática. Começamos explicitando que o fatorial de 0 é 1. Então dizemos que o fatorial de qualquer número inteiro positivo é ele multiplicado pelo fatorial do predecessor. Assim fica traduzido para o idioma Haskell.

Essa é a primeira vez que definimos uma função recursiva. Resursão é importante em Haskell e iremos ver com calma mais tarde. Mas para dar água na boca, veremos agora o que acontece ao tentarmos calcular o fatorial de, digamos, 3: tentamos descobrir o resultado de [code]3 * factorial 2[/code]. O fatorial de 2 é [code]2 * factorial 1[/code], então até agora temos [code]3 * (2 * factorial 1)[/code]. [code]factorial 1[/code] é [code]1 * factorial 0[/code], então [code]3 * (2 * (1 * factorial 0))[/code]. É ai que temos o truque &mdash; definimos o fatorial de 0 como sendo 1 com a intenção de não executar a terceira linha, então só retornamos 1. O resultado final será o equivalente a [code]3 * (2 * (1 * 1))[/code]. Se tivéssemos colocado a linha para a recursão como primeira da função, o zero seria incluido e o cálculo seria infinito. Por isso a ordem é muito importante ao definir <i>patterns</i> e é melhor colocar as mais específicas no início e deixar as gerais para o fim.

<i>Pattern matching</i> também podem falhar, como no caso de definirmos uma função como essa:

e tentarmos chamá-la com um parâmetro inesperado:

Isso acontece por não termos uma <i>pattern</i> genérica. Ao criar <i>patterns</i>, sempre devemos 
incluir <i>pattern</i> que cubra todas as excessões para que o programa não pare de modo indevido 
caso usemos um valor inesperado.

<i>Pattern matching</i> também pode ser usada em tuplas. Se precisamos fazer uma função que recebe 
dois vetores em um plano bidimensional (em forma de pares) e o some, somamos seus x primeiro e 
depois seus y. Assim seria como faríamos se não conhecessemos <i>pattern matching</i>:

Bom, funciona. Mas existe um modo muito melhor de se fazer a mesma coisa. Vamos modificar essa 
função para que passe a usar <i>pattern matching</i>.

Aí está. Muito melhor. Veja que temos um <i>pattern</i> padrão. O tipo de [code]addVectors[/code] (em ambos casos) é [code]addVectors :: (Num a) =&gt; (a, a) -&gt; (a, a) - &gt; (a, a)[/code], então garantimos que receberemos dois pares como parâmetros.

[code]fst[/code] e [code]snd[/code] também precisam de parâmetros em pares. Mas e com triplas? Não há nenhuma função que trabalhe com elas, mas podemos fazer a nossa própria.


[code]_[/code] significa o mesmo que em compreensão de listas. Se o compilador não deve se importar com o que há ali, usamos o [code]_[/code].

O que me lembra que <i>patterns matching</i> também podem ser usadas em compreensão de listas. Veja...

Se a <i>pattern</i> falhar, se moverá para o próximo elemento.

As prórias listas podem ser usadas em pattern matching. Você pode casar um padrão com uma lista vazia 
[code][][/code] ou qualquer padrão que involva [code]:[/code] e uma lista vazia. Sabendo que [code][1,2,3][/code] 
é apenas um açucar sintático para [code]1:2:3:[][/code], você também pode utilizar o padrão original. 
Uma pattern como [code]x:xs[/code] irá colocar a cabeça de uma lista em [code]x[/code] o resto dela em 
[code]xs[/code], ainda que o único elemento em [code]xs[/code] seja uma lista vazia.

Nota: O padrão [code]x:xs[/code] é muito usado, principalmente em funções recursivas. O (possível) problema é de [code]:[/code] funcionar apenas com lista de length 1 ou maior.

Se você quer deixar, digamos, os três primeiros elementos de uma lista numa variável e o resto em outra, você pode escrever algo como [code]x:y:z:zs[/code]. Só funcionará com lista de 3 ou mais elementos.

Agora que conseguimos comparar um <i>pattern</i> com uma lista, vamos criar nossa própria implementação da função [code]head[/code].

Vejamos se funciona:

Excelente! Saiba ainda que se você quiser especificar diversas variáveis (mesmo que uma delas seja [code]_[/code] e que isso não seja realmente usado em algo), deveremos coloca-las entre parênteses. Perceba também a função [code]error[/code] que usamos. Ela recebe uma string e gera um <ins>runtime error</ins>, usando a string para nos informar o erro ocorrido. Já que ele causa a finalização do programa, é bom usa-lo moderadamente. Mas chamar [code]head[/code] com uma lista vazia não faz sentido, então...

Vamos então criar uma função muito útil que nos diz os primeiros elementos de uma lista de um modo mais (des)interessante.

Essa função é considerada segura porque prevê uma lista vazia, uma lista com apenas um elemento, uma com dois e outra com mais elementos. O [code](x:[])[/code] e [code](x:y:[])[/code] poderiam ser escritos como [code][x][/code] e [code][x,y][/code] (como é algo mais simples, despensa-se os parênteses). Ao contrário de [code](x:y:_)[/code], que são para listas de mais de dois elementos e não podem ser escritos com colchetes.

Nós já implementamos a nossa própria [code]length[/code] usando <a href="starting-out#im-a-list-comprehension" target="_blank">compreensão de listas</a>. Agora tentaremos fazer usando pattern matching e um pouco de recursão:


Isso é similar à função de fatorial que escrevemos anteriormente. Primeiro definimos o resultado do valor conhecido &mdash; a lista vazia. Isso é conhecido também como <a href="http://en.wikipedia.org/wiki/Edge_case" target="_blank">edge condition</a>. Então no segundo pattern dividimos a coitada da lista em primeiro elemento e resto. Dissemos que a length da lista é igual a 1 somado com a length do "resto". Usamos [code]_[/code] para o primeiro elemento porque não nos importamos com o seu valor. Perceba que nos preocupamos com os dois lados: uma lista vazia na primeira linha e uma com mais de um elemento na segunda.

Então vamos descobrir o que acontece ao chamar [code]length'[/code] com [code]"ham"[/code]. Primeiro, é testado se é uma lista vazia. Já que não é, passa-se para o segundo pattern. Aí passamos no teste e o resultado da length é [code]1 + length' "am"[/code], porque dividimos a lista em primeiro e resto e descartamos o primeiro elemento. Até aí tudo bem. [code]length'[/code] de [code]"am"[/code] é, semelhantemente, [code]1 + length' "m"[/code]. Então agora temos [code]1 + (1 + length' "m")[/code]. [code]length' "m"[/code] é [code]1 + length' ""[/code] (que também poderia ser escrito como [code]1 + length' [][/code]). E já definimos que [code]length' [][/code] é [code]0[/code]. Logo, no fim temos [code]1 + (1 + (1 + 0))[/code].

Vamos implementar [code]sum[/code]. Sabemos que a soma de uma lista vazia deve ser 0. Escrevemos então como um pattern. E também sabemos que a soma de uma lista é o primeiro elemento mais a soma do resto da lista. Se usarmos o mesmo método, temos:

Ainda existe uma coisa chamada de <i><strong>as patterns</strong></i>. Eles são uma mão na roda quando precisamos usar um pattern e continuar com ele acessível, mesmo depois de usado. Você cria isso colocando um [code]@[/code] na frente do nome do pattern. Por exemplo, o pattern [code]xs@(x:y:ys)[/code]. Esse <i>as patterns</i> irá fazer exatamente a mesma coisa que [code]x:y:ys[/code], mas você consegue obter a lista inteira usando apenas <strong>xs</strong>, ao invés repetir tudo novamente digitando [code]x:y:ys[/code] no corpo da função novamente. Um exemplo bem simples:

Normalmente usamos esse recurso <i>as patterns</i> para evitar repetição de grandes patterns usadas durante a declaração de uma função.

Mais uma coisa - você não pode usar [code]++[/code] em patterns. Se você tentar usar pattern [code](xs ++ ys)[/code], qual seria a primeira e qual seria a segunda lista? Não faz muito sentido. Faria algum sentido usar [code](xs ++ [x,y,z])[/code] ou [code](xs ++ [x])[/code], mas pela estrutura das listas isso não é possível.
