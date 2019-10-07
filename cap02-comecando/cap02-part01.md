Começando
=========

** Preparar, apontar, foi! **

Ok, vamos começar! Se você é o tipo de pessoa horrível que não lê as introduções das coisas ou se pulou ela, então de qualquer maneira você irá querer ler a <a href="introduction#what-you-need">última seção</a> da introdução, porque nela explico o que é necessário para acompanhar este tutorial e como vamos fazer para carregar as funções. A primeira coisa é executar o GHC no modo interativo e chamar alguma função para obter uma base para começar a usar o Haskell. Abra seu terminal e digite [code]ghci[/code]. Você será agraciado com algo parecido com isto:

Meus parabéns, você esta no GHCi! O prompt aqui é [code]Prelude&gt;[/code] no entando como isto é muito longo, quando carregamos algumas coisas na sessão, iremos usar [code]ghci&gt;[/code]. Se quiser ter o mesmo prompt, digite [code]:set prompt "ghci&gt; "[/code]. 


Veja algumas simples operações aritméticas.

Isto é bastante auto-explicativo. Podemos utilizar todos operadores comuns em uma só linha e todas as regras usuais precedentes serão obedecidas. Também podemos utilizar parênteses para declarar de forma explicita a operação precedente ou para muda-la.


Muito legal hein?! Sim, eu sei que não... mas tenha paciência comigo. Uma pequena armadilha a se observar aqui é a negação de números. Se quiser obter números negativos, sempre tenha a operação determinada por parênteses. Fazendo [code]5 * -3[/code] o GHCi irá gritar com você, porém fazendo [code]5 * (-3)[/code] funcionará perfeitamente.

<a href="http://pt.wikipedia.org/wiki/%C3%81lgebra_booleana" target="_blank">Álgebra booleana</a> também é bastante simples. Você provavelmente já sabe que [code]&amp;&amp;[/code] refere-se a expressão booleana <i>and</i>, [code]||[/code] refere-se a expressão booleana <i>or</i>, e [code]not[/code] faz a negação de um [code]True[/code] ou de um [code]False[/code].

Testando igualdades é algo assim.

Que tal se fizermos [code]5 + "llama"[/code] ou [code]5 == True[/code]? Bem, se tentarmos o primeiro trecho destes código, teremos uma grande e assustadora mensagem de erro!

Credo! O que o GHCi esta dizendo aqui é que [code]"llama"[/code] não é um número e que ele não sabe como adicionar isto ao número 5. Se isso não fosse um [code]"llama"[/code], mas um [code]"four"[/code] ou um [code]"4"[/code], Haskell ainda assim não iria considerá-lo como um número. [code]+[/code] espera-se que tenha a sua esquerda e a sua direita um número.
Se tentarmos fazer [code]True == 5[/code], o GHCi avisa que os tipos são diferentes. Visto que o [code]+[/code] funciona somente em coisas consideradas números, o [code]==[/code] funciona em quaisquer coisas que podem ser comparadas. O problema é que ambos devem ser do mesmo tipo. Não podemos comparar maças com laranjas. Veremos os tipos um pouco mais adiante. 

Nota: Você pode fazer [code]5 + 4.0[/code] porque [code]5[/code] é adaptável e pode se comportar como um inteiro ou um ponto flutuante (floating-point number). [code]4.0[/code] não pode se comportar como um inteiro, então o [code]5[/code] é o único que pode ser adaptado.

Talvez você não saiba, mas estamos usando função agora o tempo todo. Por exemplo, [code]*[/code] é uma função que pega dois números e então os multiplica. Como você pode perceber, invocamos ela colocando no meio dos dois números. Isto é o que chamamos de função <i>infixa</i>. Muitas funções não são usadas com números, pois são as funções de <i>prefixo</i>. Vamos dar uma olhada então nisto. 

Funções normalmente são <i>prefixo</i>, porém agora não declararemos explicitamente que a função é do formato <i>prefixo<i>, iremos apenas assumir isto. Na maioria das <a href="http://pt.wikipedia.org/wiki/Programa%C3%A7%C3%A3o_imperativa">linguagens imperativas</a> as funções são chamadas escrevendo o nome da função e então escrevendo os seus parâmetros entre parênteses, normalmente separados por vírgula. Em Haskell, as funções são chamadas escrevendo o nome da função, com um espaço e então os parâmetros separados por espaços. Inicialmente, vamos tentar chamar uma das mais chatas funções em Haskell.


A função [code]succ[/code] pega qualquer coisa e então define o seu sucessor e o retorna. Nesse exemplo, apenas separamos o nome da função dos parâmetros com um espaço. Chamar a função com alguns parâmetros também é bastante simples. As funções [code]min[/code] e [code]max[/code] pegam duas coisas que podem ser colocadas em ordem (como um número!) e retorna aquele que for o menor ou maior. Veja você mesmo:


Aplicar uma função (chamando a função colocando um espaço depois dela, e depois ir digitando os seus parâmetros) tem sempre a maior precedência. Isto significa que estas duas declarações são equivalentes.

No entanto, se quisermos ter o sucessor do produto dos números 9 e 10, não podemos escrever [code]succ 9 * 10[/code] senão isto irá pegar o sucessor do 9, que seria multiplicado por 10. Resultando em 100. Devemos escrever [code]succ (9 * 10)[/code] para obter 91.

Se a função recebe dois parâmetros, podemos também chamá-la como uma função <i>infixa<i/> colocando antes dela aspas simples. Por exemplo, se a função [code]div[/code] recebe dois inteiros e realiza a divisão deles. Fazendo [code]div 92 10[/code] o resultado será 9. Porém quando realizamos este tipo de chamada ela fica meio confuso saber qual número do dividendo e qual é o divisor. Podemos invocar esta função como uma função <i>infixa</i> fazendo [code]92 `div` 10[/code] e logicamente isto ficará muito mais claro.

Muitas pessoas que vieram de linguagens imperativas tendem a colocar a notação dos parênteses para especificar o uso de funções. Por exemplo, em <a href="http://pt.wikipedia.org/wiki/C_%28linguagem_de_programa%C3%A7%C3%A3o%29" target="_blank">C</a>, usamos parênteses para chamar funções como [code]foo()[/code], [code]bar(1)[/code] ou [code]baz(3, "haha")[/code]. Como dissemos, espaços são utilizados para a aplicação de funções em Haskell. Então estas funções em Haskell deveriam ser [code]foo[/code], [code]bar 1[/code] e [code]baz 3 "haha"[/code]. Se encontrar algo como [code]bar (bar 3)[/code] não significa que aquela chamada de [code]bar[/code] recebe [code]bar[/code] e [code]3[/code] como parâmetros. Isto significa que primeiros chamamos a função [code]bar[/code] com o parâmetro [code]3[/code] para obter algum número e então chamar [code]bar[/code] novamente com este número. Em C, isso seria algo como [code]bar(bar(3))[/code].
