Functors, Applicative Functors e Monoids
========================================

A combinação de pureza em Haskell, funções de ordem superior, tipos de dados algébricos parametrizados e
typeclasses nos permite implementar polimorfismo em um nível muito mais alto em relação as outras
linguagens. Não precisamos pensar sobre a dependência dos tipos em uma grande hierarquia de tipos.
Ao invés disso, pensamos a respeito de como os tipos podem se comportar e se conectar com as typeclasses
apropriadas. Um [code]Int[/code] pode se comportar como um monte de coisas. Ele pode se comportar como
algo que compara a igualdade de coisas, como algo que ordena, como algo que enumera as coisas, etc.

As typeclasses são abertas, o que nos permite definir os nossos próprios tipos de dados, pense sobre como
algo deve se comportar e conecte isso com as typeclasses que definem esse comportamento. Por causa disso
e por causa do belo sistema de tipos de Haskell, que nos permite saber bastante sobre uma função apenas
olhando para a sua declaração de tipo, podemos definir typeclasses que definem um comportamento bem 
amplo e abstrato. Nós já fomos apresentados a typeclasses que definem as operações que inspecionam 
duas coisas quaisquer e nos dizem se elas são iguais ou que comparam a ordem delas. Esses são 
comportamentos bastante abstratos e elegantes, mas nós já não pensamos neles como algo super ultra especial porque geralmente lidamos com eles ao longo de boa parte das nossas vidas. Recentemente nós descobrimos os <i>functors</i>, que basicamente são coisas que podem ser mapeadas. Isso é um exemplo de 
uma propriedade bastante útil e abstrata que as typeclasses podem descrever. Neste capítulo vamos
dar uma olhada bem de perto em <i>functors</i>, juntamente com uma versão mais forte e útil de <i>functors</i> chamada de <i>applicative functors</i>. Vamos também dar uma boa olhada em <i>monoids</i>, que são uma espécie de isolante.