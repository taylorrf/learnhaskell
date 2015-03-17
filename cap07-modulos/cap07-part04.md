Data.Map
========

Listas de associação (também chamadas de dicionários) são listas usadas para armazenar pares de 
chave e valor, onde sua ordem não é considerada. Por exemplo, podemos usar uma lista de associação para 
guardar números de telefone (que seriam os valores) junto do nome dos donos (chaves). A ordem que 
esses dados estão armazenados não importa para nós, desde que consigamos o número certo ao pesquisar 
por uma pessoa.

O modo mais óbvio de armazenar listas de associação em Haskell seria uma lista de pares. Onde o primeiro 
componente do par deve ser a chave, e o segundo componente o valor. Aqui vai um exemplo de uma lista de 
associação com números de telefone:


Tirando a identação bizarra, isso é apenas uma lista de pares de strings. O objetivo mais comum ao lidar com 
listas de associação é a busca por chave. Vamos criar uma função que procura por uma dada chave.


Extremamente simples. A função recebe uma chave e uma lista, filtra a lista para que permaneçam apenas 
os que têm chave correspondente e retorna o primeiro par chave-valor. Mas o que acontece ao procurarmos 
por uma chave inexistente? Bem, nós tentaríamos pegar o primeiro elemento de uma lista vazia, 
gerando um <i>runtime error</i>. Mas precisamos fazer os nossos programas para que sejam um pouco mais difíceis 
de quebrar, então usaremos o tipo [code]Maybe[/code]. Se não encontrar uma chave, retornará 
[code]Nothing[/code]. Se encontrar, retornará [code]Just alguma-coisa[/code], onde "alguma-coisa" é o 
valor correspondente à chave.


Preste atenção à declaração de tipo. Ela recebe uma chave que pode ser igualada, uma lista de associação e 
talvez devolva um valor. Parece correto.

Essa é uma função de agenda de contatos que trabalha com uma lista. Temos a condição limite, 
quebramos a lista em uma cabeça e uma cauda, temos chamadas recursivas, esta tudo ali. 
Esse é o caso clássico de uso do padrão de folding. Vamos tentar implementar então com folds.


<em>Dica:</em> Geralmente é melhor usar folds para listas de padrões recursivos ao invés de escrever 
explicitamente a recursão porque é mais fácil de se ler. Todos sabem o que é um fold ao ver a chamada à 
função [code]foldr[/code], mas toma mais tempo para entender todo seu processo de recursão.


Funciona que é uma beleza! Agora só falta você usar sua nova agenda para ver se não fica só na pesquisa.

Nós acabamos de implementar a função [code]lookup[/code] do [code]Data.List[/code]. Se quisermos 
achar um valor correspondente a uma chave, precisamos percorrer todos os elementos até encontrá-lo. 
O módulo [code]Data.Map[/code] oferece listas de associação muito mais rápidas (porque elas são 
implementadas internamente com árvores) e ainda provê algumas funções úteis. De agora em diante 
usaremos mapas ao invés de listas de associação.

Já que [code]Data.Map[/code] exporta funções que conflitam com outras de [code]Prelude[/code] e 
[code]Data.List[/code], faremos uma importação qualificada.


Coloque esse comando para importação no script e carregue-o via GHCI.

Chega de enrolar e vamos ver o que o [code]Data.Map[/code] nos guarda! Aí vai um resumo rápido de 
suas funções.

A função [function]fromList[/function] recebe uma lista de associações (na forma de uma lista mesmo) 
e retorna um mapa com a mesma estrutura.


Se existirem chaves duplicadas na lista de associação original, as duplicadas são descartadas. 
Essa é a assinatura de tipo de [code]fromList[/code].


Ela diz que <i>fromList</i> recebe uma lista de pares de tipos [code]k[/code] e [code]v[/code] e retorna um mapa com 
chaves de tipo [code]k[/code] e tipo [code]v[/code]. Perceba que quando você está criando listas 
de associação com listas, as chaves só precisam ser possíveis de serem igualadas (terem tipos 
pertencentes à typeclass [code]Eq[/code]) mas não necessariamente ordenáveis. Essa é a essência 
do módulo [code]Data.Map[/code]. Ele precisa que as chaves sejam ordenáveis para organizá-las na 
forma de uma árvore.

É sempre aconselhável usar o [code]Data.Map[/code] para associações chave-valor, a menos que o tipo 
não esteja na typeclass [code]Ord[/code].

[function]empty[/function] representa um mapa vazio. Não necessita de nenhum argumento, só retorna 
um mapa vazio.


[function]insert[/function] recebe uma chave, um valor e um mapa e retorna um novo mapa igual ao 
antigo, mas com chaves e valor adicionados.


Nós podemos implementar nosso próprio [code]fromList[/code] usando um mapa vazio, 
[code]insert[/code] e um fold. Veja só:


Um fold bastante simples. Começamos com um mapa vazio e dobramos à direita, inserindo os pares de 
chave-valor no acumulador em cada passo.

[function]null[/function] testa se um mapa está vazio.


[function]size[/function] reporta o tamanho de um mapa.


[function]singleton[/function] recebe uma chave e um valor e cria um mapa com apenas esses dados.


[function]lookup[/function] funciona como o [code]Data.List[/code] [code]lookup[/code], mas 
opera apenas em mapas. Retorna [code]Just something[/code] se encontrar algo pela chave procurada, 
[code]Nothing[/code] se encontrar nada.

[function]member[/function] é um predicado que recebe uma chave e um mapa e diz se a chave existe 
ou não no mapa.


[function]map[/function] e [function]filter[/function] funcionam semelhantemente aos homônimos 
das listas.



[function]toList[/function] é o inverso da [code]fromList[/code].


[function]keys[/function] e [function]elems[/function] retornam listas de chaves e valores, 
respectivamente. [code]keys[/code] é o equivalente a [code]map fst . Map.toList[/code] e 
[code]elems[/code] é o de [code]map snd . Map.toList[/code].

[function]fromListWith[/function] é uma função muito legal. Age como [code]fromList[/code], mas 
ao invés de discartar as chaves duplicadas, recebe também uma função que decide o que fazer com elas. 
Imagine que uma garota pode ter vários números e temos uma lista de associação como essa:


Agora se quisermos usar [code]fromList[/code] para criar um mapa, perderemos alguns números! Então é 
assim que vamos fazer:


Se uma chave duplicada for encontrada, a função que passamos será usada para combinar os valores das 
chaves em outro valor. Poderíamos primeiro ter listas únicas para os valores e usarmos [code]++[/code] 
para fazer a combinação.


Muito elegante! Outro caso de uso é quando criamos um mapa com base em uma lista de associação de números 
e quando encontramos uma chave duplicada, queremos o maior valor da chave para mante-lo.


Ou podemos optar por adicionar valores junto a mesma chave.


[function]insertWith[/function] é para [code]insert[/code] o que [code]fromListWith[/code] é para 
[code]fromList[/code]. Ele insere um par chave-valor em um mapa, porém se o mapa já tiver a chave, 
ele usa a função passada para determinar o que fazer.


Estas são apenas algumas funções do [code]Data.Map[/code]. Você pode ainda ver uma lista completa de funções na 
<a href="http://www.haskell.org/ghc/docs/latest/html/libraries/containers/Data-Map.html#v%3Aassocs">documentação</a>.
