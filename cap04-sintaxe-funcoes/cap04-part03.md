Onde!?
======

Na seção anterior, definimos uma função de calculadora de <acronym title="Índice de massa corporal">IMC</acronym> que era algo parecido com isso:


Note ainda que nos repetimos três vezes. Nós nos repetimos três vezes. Repetir-se (três vezes) no código é tão desejável quando levar um chute bem no meio da testa. Já que repetimos a expressão três vezes, seria melhor se calculássemos apenas uma vez, gravássemos numa variável para usarmos nos próximos comandos ao invés da expressão. Para isso, modificamos nossa função para isso:


Colocamos a palavra-chave [code]where[/code] (geralmente identamos até onde estão os pipes) e definimos variáveis ou funções. Esses nomes são visíveis dentro dos guards e nos permitem não ficar nos repetindo. Se decidirmos que iremos calcular o IMC de um modo diferente, precisamos mudar apenas uma vez. Podemos avançar um pouco e deixar nossa função assim:



Os nomes criação na seção where só são visíveis dentro da função, então não temos de nos preocupar com elas poluindo o namespace de outras funções. Note também que todos os nomes foram alinhados na mesma coluna. Se não o fizéssemos, Haskell ficaria confuso e não saberia que eles fazem parte do mesmo bloco.

Associações <i>where</i> não são compartilhadas entre diferentes patterns. Se você quiser que vários patterns de uma mesma função compartilhem um determinado nome, você deverá especificá-los como global.

Você também pode usar <i>where</i> em conjunto com <em>pattern match</em>! Poderíamos reescrever a parte <i>where</i> da nossa função anterior como:


Vamos fazer agora outra função extremamente necessária que recebe nome e sobrenome e retorna suas iniciais.


Poderíamos fazer esse <i>pattern matching</i> diretamente nos parâmetros da função (o que resultaria em um código mais limpo) mas é só para mostrar que é possível também fazer isso usando o <i>where</i>.

Assim como definimos constantes em blocos where, você também pode definir funções. Voltando ao nosso tema de programação saúdavel, vamos fazer uma função que pega uma lista de pesos/altura e retorna sua lista de IMC.


E por hoje é só! A razão pela qual demos o [code]IMC[/code] como exemplo de função é que não poderíamos calcular diretamente nos seus parâmetros.  Se vermos a lista passada pela função, veremos que cada par possui um IMC diferenciado.

Associações <i>where</i>  também podem ser aninhadas. É comum criar uma função e definir algum helper com suas cláusulas com funções e daí criar funções helper com suas próprias cláusulas  <i>where</i>.
