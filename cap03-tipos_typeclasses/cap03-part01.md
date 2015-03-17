Tipos e Typeclasses
===================

** Acredite no tipo **

Já falamos que Haskell possui um sistema de tipos estático. O tipo de toda expressão é conhecido na hora da compilação, o que resulta num código mais seguro. Se você escrever um programa que tente dividir um tipo booleano por um número, ele nem compilará. Isso é bom porque é melhor detectarmos erros logo ao terminar de programar do que se deparar com travamentos indesejados. Tudo em Haskell tem um tipo, então o compilador pode considerar várias possibilidades antes mesmo de compilá-lo.

Ao contrário de Java ou Pascal, Haskell tem inferência de tipo. Se for digitado um número, não precisamos avisar ao Haskell que é um número. Ele consegue identificar isso automaticamente, não damos os tipos de funções e expressões explicitamente. Nós veremos apenas o básico de Haskell com uma visão apenas superficial sobre tipos. No entanto, entender o sistema de tipos é muito importante para o aprendizado de Haskell.

Um "tipo é algo como uma etiqueta que toda expressão têm. Nos diz em qual categoria ela se encaixa. A expressão [code]True[/code] é booleana, [code]"hello"[/code] é uma String, etc.

Agora usaremos o GHCI para descobrir os tipos de algumas expressões. Faremos isso usando o comando [code]:t[/code] que, seguido de qualquer expressão válida, retorna o seu tipo. Vamos dar uma olhada.

Fazendo isso, vemos que [code]:t[/code] mostra a expressão seguida de [code]::[/code] e seu tipo. [code]::[/code] pode ser lido como "do tipo". Tipos explícitos sempre são referidos pela sua primeira letra maiúscula. [code]'a'[/code], como é visto, é do tipo [code]Char[/code]. Não é difícil de identificar que vem da palavra <i>caracter</i>. [code]True[/code] é [code]Boolean[/code]. Faz sentido. Mas, o que é isso? Examinando o tipo de [code]"HELLO!"[/code] descobrimos que ele é [code][Char][/code]. Os colchetes denotam uma lista. Então lemos isso como uma <i>lista de caracteres</i>. Ao contrário de listas, cada valor da tupla tem seu tipo. Então a expressão [code](True, 'a')[/code] tem o tipo [code](Bool, Char)[/code], e uma expressão como [code]('a','b','c')[/code] deverá retornar [code](Char, Char, Char)[/code]. [code]4 == 5[/code] sempre retornará [code]False[/code], que é do tipo [code]Bool[/code].


Funções também têm tipos. Quando escrevemos nossas próprias funções, podemos declarar explicitamente quais são os seus tipos. Isso geralmente é considerado uma boa prática exceto quando a função é muito curta. Daqui pra frente daremos vários exemplos que fazem uso de declaração de tipos. Você ainda se lembra daquela compreensão de lista que criamos no capítulo anterior e que usava filtros para retornar somente a parte maiúscula de uma string? Então, aqui esta ela novamente com os tipos declarados.


[code]removeNonUppercase[/code] tem o tipo [code][Char] -&gt; [Char][/code], o que significa que ele parte de uma string e chega em outra string. Ou seja, ele irá receber como parâmetro uma string e nos devolver outra string como resultado. O tipo [code][Char][/code] é sinônimo de [code]String[/code] então ficaria mais claro se fosse escrito [code]removeNonUppercase :: String -&gt; String[/code]. Não precisariamos fazer essa declaração de tipo para o compilador porque ele poderia inferir por si só que essa função recebe uma string e retorna outra string. E se nós precisarmos de uma função que recebe vários tipos como parâmetro? Vamos ver então uma função bem simples que pega três inteiros e os soma:


Os parâmetros são separados por [code]-&gt;[/code] e não há nenhuma distinção entre tipos de parâmetros e retorno. O tipo do retorno é o último e os parâmetros são os três primeiros. Mais adiante veremos o porquê deles serem apenas separados por um [code]-&gt;[/code] ao invés de ter um destaque maior como [code]Int, Int, Int -&gt; Int[/code] ou algo do gênero.

Se você deseja especificar o tipo da função, mas não tem certeza de qual deve ser, você pode escrevê-la normalmente e depois descobrir com o [code]:t[/code]. Funções também são expressões, então [code]:t[/code] funciona sem problemas.

Aqui temos um apanhado geral dos principais tipos.

[type]Int[/code] é inteiro. É usado por números inteiros. [code]7[/code] pode ser um [code]Int[/code] mas [code]7.2[/code] não. [code]Int[/code] possui limitações de tamanho, o que significa ter um máximo e um mínimo. Geralmente computadores 32bit têm um [code]Int[/code] máximo de 2147483647 e um mínimo de -2147483648.

[type]Integer[/code] significa, hmmm... inteiro também. A principal diferença é que não tem limitações e pode ser usado por números realmente grandes. Digo, extremamente grandes. Contudo, [code]Int[/code] é mais eficiente.


[type]Float[/code] é um número real em ponto flutuante de precisão simples.

[type]Double[/code] é um número real em ponto flutuante com o dobro(!) de precisão.

[type]Bool[/code] é um tipo booleano. Pode ter apenas dois valores: [code]True[/code] ou [code]False[/code].

[type]Char[/code] representa um caractere. É delimitado por aspas simples. Uma lista de caracteres é denominada String.

Tuplas são tipos mas possuem uma variação de acordo com a quantidade e valores que contém, então teoricamente temos tuplas com infinitos tipos, o que é demais para cobrir nesse tutorial. Note que uma tupla vazia [code]()[/code] é também um tipo e pode assumir apenas um valor: [code]()[/code]