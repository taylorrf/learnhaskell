Instâncias derivadas
====================

Na sessão <a href="types-and-typeclasses#typeclasses-101">Basicão de Typeclasses</a>, nós mostramos as bases das typeclasses. Nós mostramos que a typeclass é uma espécie de interface que define algum comportamento. Um tipo pode criar uma <em>instância</em> de uma typeclass se ele suportar esse comportamento. Exemplo: o tipo [code]Int[/code] é uma instância da typeclass [code]Eq[/code] pois a typeclass [code]Eq[/code] define o comportamento de coisas que podem ser comparadas. E como inteiros podem ser comparados, [code]Int[/code] é parte da typeclass [code]Eq[/code]. A real utilidade vêm com funções que agem como uma interface para [code]Eq[/code], chamada [code]==[/code] e [code]/=[/code]. Se um tipo é parte da typeclass [code]Eq[/code], nós podemos usar a função [code]==[/code] com valores desse tipo. Esse é porque expressões como [code]4 == 4[/code] e [code]"foo" /= "bar"[/code] tem os tipos checados.

Nós também mencionamos que eles são geralmente confundidos com classes em linguagens como Java, Python, C++ e similares, o que confunde muitas pessoas. Nessas linguagens, classes são como um modelo pelo qual nós criamos objetos que contém estado e pode realizar algumas ações. Typeclasses são mais como interfaces. Nós não construímos dados a partir de typeclasses. Em vez disso, primeiro construímos nosso tipo de dado e então nós pensamos como ele pode agir. Se ele pode agir como algo que pode ser comparado, nós fazemos dele uma instância da typeclass [code]Eq[/code. Se ele pode agir como algo que pode ser ordenado, nós fazemos dele uma instância da typeclass [code]Ord[/code].

Na próxima sessão, vamos dar uma olhada como podemos manualmente fazer nossas instâncias de tipos de uma typeclass implementando funções definidas pela typeclass. Mas por enquanto vamos ver como Haskell pode automaticamente fazer do nosso tipo uma instância de alguma das seguintes typeclasses: [code]Eq[/code], [code]Ord[/code], [code]Enum[/code], [code]Bounded[/code], [code]Show[/code], [code]Read[/code]. Haskell pode derivar o comportamento dos nossos tipos nesse contexto se nós usarmos a palavra reservada <i>deriving</i> quando construímos o nosso tipo.

Considere este tipo de dados:



Ele descreve uma pessoa. Vamos assumir que não existe duas pessoas com a mesma combinação de primeiro nome, sobrenome e idade. Agora, se nós temos registros de duas pessoas, faz algum sentido ver se eles representam a mesma pessoa? Sim, faz. Nós podemos tentar comparar ambas e ver que elas são ou não iguais. É por isso que não faz sentido para esse tipo fazer parte da [code]Eq[/code]. Nós vamos derivar a instância.


Quando nós derivamos uma instância de [code]Eq[/code] para um tipo e tentamos comparar os dois valores desse tipos com [code]==[/code] ou [code]/=[/code], Haskell verá se o valor dos construtores são correspondentes (há apenas um único construtor cujo valor passa) e então ele irá checar se todos os dados internos correspondem testando cada par de campos com [code]==[/code]. Há apenas um problema, os tipos de todos os campos também tem de fazer parte da typeclass [code]Eq[/code]. Mas uma vez que ambos são [code]String[/code] e [code]Int[/code], então OK. Vamos testar nossa instância de [code]Eq[/code].



Claro, uma vez que [code]Person[/code] agora está em [code]Eq[/code], nós podemos usá-lo como [code]a[/code] para todas as funções que tem a restrição de class para [code]Eq a[/code] em sua assinatura, como [code]elem[/code].



As typeclasses [code]Show[/code] e [code]Read[/code] são para coisas que podem ser convertidas de e para strings, respectivamente. Assim como [code]Eq[/code], se o construtor de um tipo tiver campos, seu tipo precisa fazer parte de [code]Show[/code] ou [code]Read[/code] se nós quisermos fazer do nosso tipo uma instância deles. Vamos fazer nosso tipo de dados [code]Person[/code] também fazer parte de [code]Show[/code] e [code]Read[/code].



Agora nós podemos imprimir uma pessoa no terminal.



Se nós tentássemos imprimir uma pessoa no terminal antes de tornar o tipo [code]Person[/code] parte de [code]Show[/code], Haskell teria reclamado para nós, afirmando que ele não sabia como representar uma pessoa como uma string. Mas agora que nós derivamos uma instância de [code]Show[/code] para ela, ele saberá como.

[code]Read[/code] é praticamente a typeclass inversa à [code]Show[/code]. [code]Show[/code] é para conversão de valores de um tipo em uma string, [code]Read[/code] é para conversão de strings para valores de um tipo. Lembre-se, quando nós usamos a função [code]read[/code], nós temos que usar uma notação de tipo explicita para dizer ao Haskell qual o tipo que nós queremos pegar como resultado. Se nós não explicitamos o tipo que queremos como resultado, Haskell não vai saber que tipo nós queremos.


Se usarmos depois o resultado do [code]read[/code] de um jeito que Haskell possa inferir que isto deve ser uma pessoa, nós não temos que usar nenhuma notação de tipo.



Também podemos ler tipos parametrizados, mas nós temos que preencher os tipos dos parâmetros. Então não podemos fazer [code]read "Just 't'" :: Maybe a[/code], porém podemos fazer [code]read "Just 't'" :: Maybe Char[/code].

Instâncias derivadas de [code]Ord[/code] agem como o esperado. Primeiro os construtores são comparados lexicograficamente e se o valor dos dois construtores forem os mesmos, seus campos são comparados, admitindo que os tipos dos seus campos também são instâncias de [code]Ord[/code].  O tipo [code]Bool[/code] pode ter o valor tanto de [code]False[/code] ou [code]True[/code]. Com a finalidade de ver como ele se comporta quando comparado, nós podemos pensar que ele é implementado da seguinte forma:


Por causa do construtor de valor [code]False[/code] ser especificado primeiro e o construtor de valor [code]True[/code] ser especificado logo em seguida, nós podemos considerar [code]True[/code] como maior que [code]False[/code].



No tipo [code]Maybe a[/code], o construtor de valor [code]Nothing[/code] é especificado antes do construtor de valor [code]Just[/code], portanto o valor de [code]Nothing[/code] é sempre menor que o valor de [code]Just algumacoisa[/code], mesmo que algumacoisa seja menos um bilhão de trilhões. Mas se nós compararmos dois valores [code]Just[/code], então será comparado com o que há dentro deles.



Mas nós não podemos fazer algo como [code]Just (*3) > Just (*2)[/code], pois [code](*3)[/code] e [code](*2)[/code] são funções que não são instâncias de [code]Ord[/code].

Nós podemos facilmente usar tipos algébricos para fazer enumerações e as typeclasses [code]Enum[/code] e [code]Bounded[/code] nos ajudam com isso. Considere o seguinte tipo: 


Por causa de todos os valores de construtores serem nulos (não pegam parâmetros, ou seja, campos), nós podemos fazer com que sejam parte da typeclass [code]Enum[/code]. A typeclass [code]Enum[/code] é pra coisas que tem predecessor e sucessor. Nós podemos faze-los também parte da typeclass [code]Bounded[/code], que é para coisas que tem um menor e um maior valor possível. E enquanto estamos no assunto, vamos torná-lo uma instância de todos as outras typeclasses derivadas e ver o que podemos fazer com ele.


Por ele ser parte das typeclasses [code]Show[/code] e [code]Read[/code], nós podemos converter os valores desse tipo, de e para strings.


Por ele ser parte das typeclasses [code]Eq[/code] e [code]Ord[/code], nós podemos comparar ou equiparar dias.


Ele também é parte de [code]Bounded[/code], portanto nós podemos pegar o menor e o maior dia.



Ele também é uma instância de [code]Enum[/code]. Nós podemos pegar os predecessores e sucessores dos dias e fazer listas de intervalos com eles.



Isso é bem impressionante.