Módulos
=======

** Carregando módulos **

Um módulo Haskell é uma coleção de funções, tipos e typeclasses. Um programa Haskell é uma 
coleção de módulos, onde o módulo principal carrega outros e usa suas funções para fazer algo de útil. 
Ter o código dividido em vários módulos tem várias vantagens. Se o módulo é genérico o bastante, 
quando exportadas, suas funções podem ser úteis a um bando de softwares. Se o seu próprio código 
está separado em módulos que não requerem uns aos outros (muito), eles podem ser utilizáveis em 
outros projetos. É uma grande vantagem perder algum tempo separando seu código em algumas partes 
com propósitos adequados.

A biblioteca padrão Haskell é dividida em módulos, que contém tipos e funções semelhantes ou que têm 
um uso semelhante. Existe um módulo para manipulação de listas, um módulo para programação concorrente, 
um módulo para lidar com números complexos, etc. Todas as funções, tipos e typeclasses que trabalhamos 
até agora fazem parte do módulo [code]Prelude[/code], que já é importado automaticamente. 
Nesse capítulo, daremos uma olhada em alguns módulos úteis e suas funções. Mas primeiro, como importar 
módulos.

A sintaxe de importação módulos em um script Haskell é [code]import &lt;nome do módulo&gt;[/code]. 
Isso deve ser feito antes de definir qualquer função, por isso importações são feitas geralmente no 
início do arquivo. Obviamente um script pode importar vários módulos, só é preciso colocar o código 
apropriado em linhas distintas. Então vamos importar o módulo [code]Data.List[/code] que possui 
várias funções úteis para se trabalhar com listas, inclusive uma que servirá para nos dizer quantos 
elementos únicos existem numa dada lista.


Quando você dá um [code]import Data.List[/code], todas as funções de [code]import Data.List[/code] 
tornam-se disponíveis no namespace global, o que significa que a partir daí podemos usar qualquer uma 
delas no momento que quisermos. [code]nub[/code] é uma das funções definidas em [code]Data.List[/code] 
que recebe uma lista e retira todos os elementos repetidos. Misturar [code]length[/code] e 
[code]nub[/code] fazendo [code]length . nub[/code] resulta em uma função equivalente a 
[code]\xs -&gt; length (nub xs)[/code].

Você também pode colocar funções de módulos no global namespace no GHCI. Se estiver no GHCI e 
quiser chamar uma das funções de [code]Data.List[/code], você pode fazer:


Se o que você quer é carregar vários módulos dentro do GHCI, não é necessário digitar 
[code]:m +[/code] milhares de vezes porque há a possibilidade de fazer o mesmo em apenas uma linha.


Contudo se já carregou um script que carrega módulos, não é necessário usar [code]:m +[/code] novamente.

Se seu interesse é em apenas meia dúzia de funções, você pode importar apenas elas. Para carregar 
somente a [code]nub[/code] e a [code]sort[/code] do [code]Data.List[/code], fazemos assim:


Você ainda pode importar - com exceções - todas as funções de um módulo. Isso é útil quando módulos 
exportam funções de mesmo nome e você não quer sofrer com conflitos. Já que já falamos da função 
[code]nub[/code], vamos importar todas as funções do [code]Data.List[/code] menos ela:


Outro meio de lidar com conflitos de nome é qualificar importações. O módulo [code]Data.Map[/code] 
que provê formas de pesquisar valores por chaves em estruturas de dados, exporta várias funções de 
nomes repetidos como [code]Prelude[/code], [code]filter[/code] e [code]null[/code]. Então quando 
importamos [code]Data.Map[/code] e chamamos [code]filter[/code], Haskell não sabe qual função você quer. 
E é assim que resolvemos esse problema:


Isso quer dizer que quando quisermos referenciar a [code]filter[/code] do [code]Data.Map[/code], 
temos que colocar [code]Data.Map.filter[/code], e que [code]filter[/code] continua sendo nosso 
[code]filter[/code] já conhecido e amado. O problema é que digitar [code]Data.Map[/code] na frente de 
cada função que pode dar problema é um pouco trabalhoso. Por isso que também podemos renomear 
importações qualificadas para algo mais curto:


Agora para referenciar a [code]filter[/code] do [code]Data.Map[/code] fica apenas [code]M.filter[/code].

Leia essa <a href="http://www.haskell.org/ghc/docs/latest/html/libraries/">ótima referência</a> 
para ver quais módulos vêm na biblioteca padrão. Um bom modo de aprender algo novo de Haskell é 
mergulhar na biblioteca padrão para descobrir módulos e funções carregadas automaticamente. Você ainda 
pode ler seus códigos-fonte. Esse também é um dos melhores jeitos de aprender e adquirir um 
conhecimento sólido em Haskell.

Para pesquisar sobre e ver a localização de funções, use o 
<a href="http://haskell.org/hoogle">Hoogle</a>. Nesse incrível sistema de busca de Haskell, 
você pode pesquisar por nome, módulo ou até mesmo declarações de tipo.
