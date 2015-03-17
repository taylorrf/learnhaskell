Recursão
========

** Olá recursão! **

Nós já mencionamos recursão brevemente no capítulo anterior. Nesse capítulo, veremos recursão 
mais de perto, porque ela é importante em Haskell por nos permitir construir soluções muito concisas e 
elegantes para problemas pensando recursivamente.

Se você ainda não sabe o que é recursão, leia essa frase do ínicio novamente. Hahaha! Estou só brincando! 
Recursão é na verdade uma forma de definir funções onde ela própria é usada em sua definição. Geralmente 
definições em matemática são dadas recursivamente. Por exemplo, a sequência Fibonacci é definida 
recursivamente. Primeiro, definimos explicitamente os primeiros dois números de Fibonacci. 
Dizemos que <i>F(0) = 0</i> e <i>F(1) = 1</i>, o que significa que o 0-ésimo e primeiro números de 
Fibonacci são 0 e 1, respectivamente. Então dizemos que, para qualquer outro número natural, aquele 
número de Fibonacci será a soma dos dois números de Fibonacci anteriores. Então <i>F(n) = F(n-1) + F(n-2)</i>. 

Dessa forma, <i>F(3)</i> é <i>F(2) + F(1)</i>, que é <i>(F(1) + F(0)) + F(1)</i>. Como nós agora chegamos 
a somente números de Fibonacci definidos não-recursivamente, nós podemos seguramente dizer que 
<i>F(3)</i> é 2. Um ou dois elementos definidos não-recursivamente numa definição recursiva 
(como <i>F(0)</i> e <i>F(1)</i> aqui) são chamados também de <em>condições limites</em> e são 
importantes caso você queira que sua função recursiva termine em algum momento. Se não tivéssemos 
definido <i>F(0)</i> e <i>F(1)</i> não-recursivamente, você nunca encontraria número algum na solução, 
já que chegaria ao 0 e então continuaria nos números negativos. De repente, você estaria dizendo que 
<i>F(-2000)</i> é <i>F(-2001) + F(-2002)</i> e ainda assim não haveria um fim à vista!

Recursão é importante em Haskell porque, diferentemente das linguagens imperativas, você faz 
computações em Haskell declarando o que alguma coisa <b>é</b> ao invés de declarar <b>como</b> você 
chegou a ela. Por essa razão não há laços <i>while</i> ou <i>for</i> em Haskell. Por isso muitas vezes 
temos que usar recursão para declarar o que alguma coisa é.
