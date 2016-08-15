Tipo variável
=============

Qual você acha que é o tipo da função [code]head[/code]? Já que [code]head[/code] recebe uma lista e 
retorna o seu primeiro elemento, qual deve ser o seu tipo? Vamos descobrir!

Hmmm! O que é esse [code]a[/code]? É o tipo? Lembre-se que já vimos que o tipo é escrito com a primeira 
letra maiúscula, então não pode ser exatamente o tipo. E é exatamente essa diferença que nos diz ser 
um <em>tipo variável</em>. Isso significa que o [code]a[/code] pode ser qualquer tipo. Isso é algo como 
os genéricos de outras linguagens, mas em Haskell é muito mais poderoso porque nos permite facilmente 
escrever funções mais genéricas caso o processamento seja o mesmo para diferentes tipos. Funções que 
possuem tipos variáveis são denominadas <em>funções polimórficas</em>. A declaração de tipo em 
[code]head[/code] diz que ele recebe uma lista de elementos de qualquer tipo e retorna um elemento dela.

Embora tipos variáveis possam ter nomes com mais de um caractere, normalmente nós damos a eles nomes 
como a, b, c, d...

Se lembra da função [code]fst[/code]? Aquela que retorna o primeiro componente de um par? Então, vamos 
examinar o seu tipo:

Examinando o tipo de [code]fst[/code], a gente vê que ele recebe uma tupla que contém dois tipos e 
retorna o primeiro elemento. É exatamente por causa disso que conseguimos usar [code]fst[/code] em 
pares que contenham quaisquer tipos. Perceba ainda que mesmo [code]a[/code] e [code]b[/code] sendo 
diferentes tipos variáveis, eles não devem ser necessariamente de tipos diferentes. A declaração 
apenas nos diz que o tipo do primeiro componente (da tupla) deve ser do mesmo que o do retorno.
