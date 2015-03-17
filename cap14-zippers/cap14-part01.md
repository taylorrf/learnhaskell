Zippers
=======

Embora a pureza de Haskell seja acompanhada de vários benefícios, ela nos faz abordar alguns problemas diferentemente do que em linguagens impuras. Por causa da transparência referencial, um valor em Haskell é igual a outro se eles representem a mesma coisa.

Então se tivermos uma árvore cheia de cincos e quisermos mudar um desses para seis, nós temos que saber exatamente qual cinco da nossa árvore nós queremos mudar. Temos que saber onde ele esta na nossa árvore. Em linguagens impuras, nós podemos só verificar na memória onde o cinco está localizado e muda-lo. Mas em Haskell, um cinco é igual ao outro, então não podemos diferencia-lo baseado na sua posição na memória. Nós também não podemos <i>alterar</i> qualquer valor; quando falamos em mudar uma árvore, isso significa que estamos recebendo uma árvore e devolvendo uma nova árvore semelhante a original, mas levemente diferente.

Uma coisa que podemos fazer é lembrar o caminho da raiz da árvore até o elemento que queremos mudar. Nós podemos dizer, pegue a árvore, vá para esquerda, vá para direita e então esquerda novamente e mude o elemento que está nessa posição. Embora isso funcione, pode ser ineficiente. Se quisermos mais tarde mudar o elemento vizinho ao elemento previamente atualizado, temos que andar todo o caminho da raiz da árvore até o nosso elemento novamente!

Nesse capítulo, nós iremos ver como podemos manipular uma estrutura de dados de forma fácil e eficiente. Legal!