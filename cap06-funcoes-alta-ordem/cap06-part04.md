Lambdas
=======

Lambdas são basicamente funções anônimas que utilizamos quando precisamos de alguma função 
somente uma vez. Normalmente, nós criamos um lambda com o propósito único de passá-lo para uma função 
de ordem superior. Para criar um lambda, digitamos um [code]\[/code] (porque isso se parece com a letra 
grega lambda caso você seja vesgo o suficiente) e então nós escrevemos os parâmetros separados por 
espaços. Depois vem o [code]-&gt;[/code] e então o corpo da função. Normalmente cercamos com parênteses, 
porque senão isso se estende para todo o lado direito.

Se você olhar 902px acima, você verá que nós utilizamos uma associação <i>where</i> em nossa função 
[code]numLongChains[/code] para fazer a função [code]isLong[/code] com o único propósito de passar 
isso para o [code]filter[/code]. Bem, ao invés de fazermos isso, podemos utilizar um lambda:

Lambdas são expressões, este é o porque de só passarmos aquilo. A expressão 
[code](\xs -&gt; length xs &gt; 15)[/code] retorna uma função que nos diz se o length da lista que foi 
passada é maior do que 15.

Pessoas que não se familiarizaram muito bem antes com o funcionamento de currying e aplicações parciais 
utilizam lambdas onde não deveriam. Por exemplo, as expressões [code]map (+3) [1,6,3,2][/code] e 
[code]map (\x -&gt; x + 3) [1,6,3,2][/code] são equivalentes desde que ambos [code](+3)[/code] e 
[code](\x -&gt; x + 3)[/code] sejam funções que peguem um número e adicione 3 nele. Não preciso dizer 
que criar um lambda neste caso seria uma estupidez já que com a utilização da aplicação parcial fica 
bem mais legível.

Como uma função normal, lambdas podem receber quantos parâmetros você quiser:

E como uma função normal, você pode utilizar um pattern match em lambdas. A única diferença é que você 
não define alguns patterns para um único parâmetro, como fazer um [code][][/code] e um pattern 
[code](x:xs)[/code] para o mesmo parâmetro e depois ter valores disso. Se um pattern matching falhar 
em um lambda, um erro de runtime ocorrerá, portanto seja cuidadoso com pattern matching em lambdas! 

Lambdas são normalmente cercadas por parênteses a não ser que você queira estende-la por todo o lado 
direito. Uma coisa interessante: como funções são curried por default, estes dois são equivalentes: 

Se definirmos uma função como essa, é óbvio que a declaração de tipo é o que é. Lá estão os 
[code]-&gt;[/code] em ambas declaração de tipo e a equação. Mas é claro, o primeiro jeito de escrever 
a função é bem mais legível enquanto o segundo é muito mais uma demonstração do modo currying.

Tanto faz, é legal quando utilizamos estas notações. Eu acho que aquela função [code]flip[/code] 
fica bem mais legível quando definimos assim: 

Apesar de isso ser o mesmo que escrever [code]flip' f x y = f y x[/code], fazemos assim obviamente 
por ser o mais utilizado na criação de uma nova função na maioria dos casos. O caso mais comum na 
utilização do [code]flip[/code] é somente chamá-lo com o parâmetro da função e então passar o 
resultado para um mapa ou um filtro. Utilize lambdas desse jeito quando você quiser deixar explicito 
que a sua função é destinada a ser parcialmente aplicada e passada em uma função como um parâmetro.
