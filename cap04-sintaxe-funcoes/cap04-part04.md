Deixe estar
===========

Muito semelhantes às associações where, existem as <strong>let</strong>. Associações where te permitem dar valores a variáveis no fim de uma função, ao mesmo tempo que permitem que toda função a veja, incluindo guards. Associações let dão valores a funções e também são expressões, mas tem um escopo mais restrito por não serem acessíveis dentro de guards. Assim como toda construção em Haskell que permite dar valores a nomes, construções let podem ser usadas em pattern matching. Vejamos em ação! Poderíamos fazer assim uma função que nos dá a área da superfície de um cilíndro baseado em sua altura e raio:


Deve-se seguir a forma [code]let &lt;bindings&gt; in &lt;expression&gt;[/code]. Os nomes definidos na parte <i>let</i> são acessíveis mesmo na expressão seguinte <i>in</i>. Como você deve ter percebido, poderíamos ter definido essa associação com um <i>where</i>. Perceba ainda que os nomes também estão alinhados na mesma coluna. Então qual é a diferença dos dois? Por hora, <i>let</i> coloca as associações antes da expressão que as usa, o contrário da <i>where</i>.

Outra diferença é que associações <i>let</i> são expressões por si próprias. <i>where</i> são apenas construções sintáticas. Lembre-se de quando vimos que a estrutura if/else é uma expressão que pode ser enfiada em qualquer lugar?


Isso também pode ser feito com associações let.


Elas ainda podem ser alternativas para criação e uso de funções de escopo local:



Se quisermos definir várias variáveis na mesma linha, obviamente não podemos alinhá-las em uma coluna. É por isso que também podemos separá-las por pontos e vírgulas.



Você não necessariamente precisa-se colocar um ponto e vírgula depois da última associação. Como já dissemos, podemos usar pattern match em associações <i>let</i>. Isso é muito útil para quebrar uma tupla e dar nomes a cada componente.



Também pode-se colocar associações <i>let</i> dentro de compreensão de listas. Vamos reescrever nosso exemplo anterior que recebe listas de pares massa-altura, usar um <i>let</i> e dispensar a definição de uma função auxiliar interna com <i>where</i>.



Incluímos o <i>let</i> dentro da compreensão de listas como predicado, mas não com a intenção de filtrar a lista, mas sim para apenas dar nomes aos elementos. Nomes definidos num <i>let</i> dentro de uma compreensão de listas são visíveis à função a que pertence (o que antecede o [code]|[/code]) e a todos os predicados e seções que seguem à associação. Então simplesmente podemos fazer nossa função retornar apenas o IMC de pessoas gordas:



Só não podemos usar o nome [code]imc[/code] em [code](w, h) &lt;- xs[/code] porque é executado antes da definição do <i>let</i>.

Omitimos o <i>in</i> do <i>let</i> porque usamos-o em uma compreensão de listas, que já pré-define a visibilidade de nomes. No entanto, poderíamos usar <i>let in</i> no predicado e seus nomes serem acessíveis apenas dentro do predicado. O <i>in</i> ainda pode ser omitido ao definir funções e constantes diretamente no GHCI. Caso o façamos, os nomes serão visíveis por toda a sessão interativa.

Você pode estar se perguntando: Se associações <i>let</i> são tão perfeitas, porque não usá-las ao invés de <i>where</i>? Bom, já que associações <i>let</i> são mais restritivas quanto ao escopo, elas não podem ser usadas entre guards. Algumas pessoas preferem usar <i>where</i> porque os nomes vêm depois da função que os usam. Desse modo, o corpo da função fica próximo de nomes e declaração de tipo, tornando mais legível.

