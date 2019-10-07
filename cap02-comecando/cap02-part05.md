Eu sou a compreensão de lista
=============================

Se você já esteve em um curso de matemática, provavelmente já viu compreensão de conjuntos. Isto é normalmente utilizado para a construção de conjuntos mais específicos de conjuntos em geral. Uma <i>compreensão</i> básica para um conjunto que contém os dez primeiros números naturais seria. A parte depois do <i>pipe</i> é chamada de output da função, [code]x[/code] é a variável, [code]N[/code] é o input e [code]x &lt;= 10[/code] o predicado. Isso significa que o conjunto contém o dobro de todos os números naturais que satisfazem o predicado.

Se quisermos escrever isto em Haskell podemos fazer algo como [code]take 10 [2,4..][/code]. Mas se não quisermos dobrar os 10 primeiros números naturais, mas sim aplicar alguma função mais complexa neles? Podemos utilizar <a href="http://pt.wikipedia.org/wiki/List_comprehension" target="_blank">compreensão de lista</a> para isto. Compreensão de lista é bastante similar a compreensão de conjuntos. Vamos começar com os 10 primeiros números pares agora. A compreensão de lista que podemos usar é [code][x*2 | x &lt;- [1..10]][/code]. [code]x[/code] é traçado a partir de [code][1..10][/code] e para cada elemento em [code][1..10][/code] (que temos vinculado a [code]x[/code]) teremos esse elemento dobrado. Aqui esta a compreensão em ação.

Como você vê, obtemos os resultados desejados. Agora vamos adicionar uma condição (ou um predicado) nesta compreensão. Predicados ficam depois dessa parte obrigatória sendo separado por vírgula. Digamos que queremos somente os elementos que, dobrados, são maiores ou igual a 12.

Legal, isso funciona. E se quisermos todos os números entre 50 e 100 onde o resto da divisão pelo número 7 fosse 3? Fácil.

Sucesso! Note que reduzir as listas por predicados é chamado de <em>filtragem</em>. Temos uma lista de números e os filtraremos pelo predicado. Vamos a outro exemplo. Digamos que você queira uma <i>compreensão</i> que substitua cada número ímpar maior do que 10 com [code]"BANG!"[/code] e cada número ímpar menor do que 10 com [code]"BOOM!"[/code]. Se o número não for ímpar, queremos ele fora da lista. Convenientemente colocamos esta <i>compreensão</i> dentro de uma função para reutilizá-la mais facilmente.

A última parte da <i>compreensão</i> é o predicado. A função [code]odd[/code] retorna [code]True[/code] quando o número for ímpar e [code]False[/code] quando for par. O elemento é incluído na lista somente se todos os predicados examinados sejam [code]True[/code].

Podemos também incluir vários predicados. Se quisermos todos os números entre 10 e 20 que não sejam 13, 15 ou 19, podemos fazer:

Não apenas podemos ter vários predicados em uma compreensão de lista (um elemento deve satisfazer todos os predicados para ser incluso no resultado da lista), como também podemos extrair várias listas. Quando extraímos diversas listas, a compreensão produz todas as combinações da lista desejada e junta com a função de output que fornecemos. Uma lista produzida pela compreensão da extração a partir de duas listas com length 4 terá um length de 16, desde que não seja filtrado. Se tiver duas listas, [code][2,5,10][/code] e [code][8,10,11][/code] e quiser obter o produto de todas as combinações possíveis entre números destas listas, aqui está o que devemos fazer.

Como esperado, o length da nova lista é 9. E se quiser todos os possíveis produtos que sejam maiores do que 50?

Que tal uma compreensão de lista que combina uma lista de adjetivos e uma lista de substantivos... só para dar umas risadas.

Já sei! Vamos escrever a nossa própria versão do [code]length[/code]! Chamaremos isto de [code]length'[/code].

[code]_[/code] significa que não me importo com o que vamos extrair da lista, ao invés de escrever o nome da variável que nunca será usada, basta escrever [code]_[/code]. Esta função substitui todos os elementos da lista com [code]1[/code] e soma todos eles. A idéia é ter o resultado da soma e obter depois o length da nossa lista.

Lembrete de amigo: como strings são listas, devemos usar compreensão de listas para processar e produzir strings. Eis uma função que recebe uma string e remove tudo exceto letras maiúsculas dela.

Testando isto:

O predicado aqui faz todo o serviço. Ele dirá ao caractere que será incluído em uma nova lista somente se for um elemento da lista [code]['A'..'Z'][/code]. Aninhar compreensões de lista também é possível se estiver operando em uma lista que contém listas. Uma lista que contém diversas listas de números. Vamos remover todos os números ímpares sem diminui-los.

Você pode escrever compreensão de lista em diversas linhas. Se não estiver no GHCI, é melhor que quebre longas compreensões de lista em multiplas linhas, especialmente se forem aninhadas.
