Aleatoriedade
=============

Muitas vezes quando se esta programando, você precisa pegar dados aleatórios. Talvez você esteja fazendo um jogo onde um dado precisa ser jogado ou que você precisa gerar alguns dados de teste para testar seu programa. Há uma série de usos para dados aleatórios enquanto programamos. Bem, atualmente, pseudoaleatório, porque todos nós sabemos que a única verdadeira fonte de aleatoriedade é um macaco em um monociclo com um queijo em uma mão e a outra na bunda. Nesta seção, vamos dar uma olhada em como fazer Haskell gerar dados aparentemente aleatórios.

Na maioria das outras linguagens de programação, você tem funções que lhe dão de volta um número aleatório. Cada vez que você chama essa função, você recebe de volta um número aleatório diferente(espero). E em Haskell? Bem, lembre, Haskell é uma linguagem funcional pura. O que isto significa é que ele tem transparência referencial. O que ISTO significa é que uma função, se dado os mesmos parâmetros duas vezes, deve produzir o mesmo resultado duas vezes. Isso é muito legal, pois nos permite raciocinar de forma diferente sobre os programas e nos permite adiar a avaliação até que realmente precisemos dela. Se eu chamar uma função, posso ter certeza que ela não vai fazer qualquer coisa engraçada antes de me dar os resultados. Tudo o que importa são os resultados. No entanto, isso faz com que seja um pouco complicado para obter números aleatórios. Se eu tenho uma função como esta:


Isso não é muito útil como uma função de número aleatório, porque ela vai sempre retornar [code]4[/code], mesmo que eu possa garantir que o 4 é completamente aleatório, porque eu usei um dado para determinar ele.

Como outras linguagens fazem números aparentemente aleatórios? Bem, elas pegam várias informações do seu computador, como a hora atual, quanto e onde você moveu o mouse e que tipo de ruídos que você fez atrás de seu computador para com base nisso lhe dar um número que parece muito aleatório. A combinação desses fatores (de aleatoriedade) é provavelmente diferente em um determinado momento no tempo, de modo a obter um número aleatório diferente.

Ah. Então em Haskell, nós podemos fazer um número aleatório, se em seguida fizermos uma função que recebe como parâmetro essa aleatoriedade e com base nisso retornar um número (ou outro tipo de dados).

Veja o módulo [code]System.Random[/code]. Ele tem todas as funções que satisfazem nossa necessidade de aleatoriedade. Vamos mergulhar em uma das funções que ele exporta depois, chamada [function]random[/function]. Este é o seu tipo: [code]random :: (RandomGen g, Random a) =&gt; g -&gt; (a, g)[/code]. Opa! Algumas typeclasses novas nessa declaração de tipo pintando por aqui! A typeclass [class]RandomGen[/class] é para tipos que podem atuar como fontes de aleatoriedade. A typeclass [class]Random[/class] é para coisas que podem assumir valores aleatórios. Um valor booleano pode assumir um valor aleatório, isto é [code]True[/code] ou [code]False[/code]. Um número também pode assumir uma infinidade de valores aleatórios diferentes. Pode uma função assumir um valor aleatório? Eu não penso assim, provavelmente não! Se tentarmos traduzir a declaração de tipo de [code]random[/code] para Inglês, teremos algo como: isto pega um gerador aleatório (essa é a nossa fonte de aleatoriedade) e retorna um valor aleatório e um novo gerador aleatório. Porque isso sempre retorna um novo gerador bem como um valor aleatório? Então, veremos em seguida.

Para utilizar nossa função [code]random[/code], temos que ter em nossas mãos um desses geradores aleatórios. O módulo [code]System.Random[/code] exporta um tipo bem legal chamado de <span class="label type">StdGen</span> que é uma instância do tipo de classe [code]RandomGen[/code]. Podemos fazer um [code]StdGen[/code] manualmente ou podemos dizer ao sistema que nos dê um baseado em uma infinidade de coisas aleatórias.

Para criar manualmente um gerador aleatório, use a função [code]mkStdGen[/code]. Ela tem o tipo [code]mkStdGen :: Int -&gt; StdGen[/code]. Ela pega um inteiro e baseada nele nos dá um gerador aleatório. Beleza então, vamos tentar usar [code]random[/code] e [code]mkStdGen[/code] em parceria para nos dar um (super randômico) número.



O que é isto? Ah, certo, a função [code]random[/code] pode retornar um valor de qualquer tipo, que faz parte da typeclass [code]Random[/code], por isso temos que informar ao Haskell que TIPO de tipo que queremos. Também não vamos esquecer que ele retorna um valor aleatório e um gerador aleatório juntos.



Finalmente! Um número que parece meio aleatório! O primeiro componente da tupla é o nosso número, enquanto que o segundo componente é uma representação textual do nosso novo gerador aleatório. O que acontece se nós chamarmos [code]random[/code] com o mesmo gerador aleatório novamente?



Claro. O mesmo resultado para os mesmos parâmetros. Vamos tentar lhe dar um gerador aleatório diferente como parâmetro.



Certo, legal, excelente, um número diferente. Podemos usar a notação de tipos para obter tipos diferentes de volta a partir da função.



Vamos fazer uma função que simula jogar uma moeda três vezes. Se [code]random[/code] não retornou um novo gerador, juntamente com um valor aleatório, nós teríamos que fazer esta função pegar três geradores aleatórios como um parâmetro e, em seguida, retornar aos lançamentos de moeda para cada um deles. Mas isso soa mal, porque se um gerador pode fazer um valor aleatório de tipo [code]Int[/code] (o qual pode assumir uma abundância de valores diferentes), ele deve ser capaz de fazer três lançamentos de moeda (que pode assumir precisamente oito combinações). Portanto, aqui é onde [code]random[/code] retornando um novo gerador, juntamente com um valor realmente vem a calhar.

Vamos representar uma moeda com um simples [code]Bool[/code]. [code]True[/code] é a cauda, [code]False[/code] é a cabeça.



Chamamos [code]random[/code] com o gerador que pegamos como parâmetro para obter uma moeda e um novo gerador. Então, nós chamamos de novo, só que desta vez com o nosso novo gerador, para obter a segunda moeda. Fazemos o mesmo para a terceira moeda. Se tivéssemos chamado com o mesmo gerador todas vezes, todas as moedas teriam o mesmo valor e nós seríamos capazes somente de obter [code](False, False, False)[/code] ou [code](True, True, True)[/code] como resultado.



Observe que nós não temos que fazer [code]random gen :: (Bool, StdGen)[/code]. Isso porque já especificamos que queremos booleanos na declaração do tipo da função. É por isso que Haskell pode inferir que queremos um valor booleano nesse caso.

Então, o que acontece se quisermos virar quatro moedas? Ou cinco? Bem, há uma função chamada [function]randoms[/function] que pega um gerador e retorna uma sequência infinita de valores com base no gerador.



Por que [code]randoms[/code] não retorna um novo gerador, assim como uma lista? Poderíamos implementar a função [code]randoms[/code] muito facilmente assim:



Uma definição recursiva. Obtemos um valor randômico qualquer e um novo gerador a partir do nosso gerador atual, então criamos uma lista contendo um valor como sendo a cabeça e um valor randômico com base no nosso novo gerador como sendo a cauda. Como precisamos ser capazes de gerar uma quantidade infinita de números, não queremos ter de volta um novo gerador randômico.

Podemos fazer uma função que gera um fluxo finito de números e um novo gerador da seguinte forma:



De novo, uma definição recursiva. Dizemos que se quisermos 0 números, apenas retornamos uma lista vazia e o gerador que nos foi dado. Para qualquer outro número de valores aleatórios, primeiro obtemos um número aleatório e um novo gerador. Essa será a cabeça. Então dizemos que a cauda será <i>n - 1</i> números gerados com o novo gerador. Então retornamos a cabeça e o resto da lista se junta e o gerador final que iremos obter será dado por <i>n - 1</i> números aleatórios.

E se quisermos um valor aleatório em algum tipo de intervalo? Todos os números inteiros aleatórios até agora foram escandalosamente grande ou pequeno. E se nós queremos para jogar um dado? Bem, usamos [function]randomR[/function] para esse propósito. Ela tem um tipo de [code]randomR :: (RandomGen g, Random a) :: (a, a) -&gt; g -&gt; (a, g)[/code], significando que é como um tipo [code]random[/code], ele pega apenas como primeiro parâmetro um par de valores que são os limites mínimos e máximos e o valor final produzido estará no intervalor desses límites.


Há também [function]randomRs[/function], que produz um fluxo de valores aleatórios dentro de nossos intervalos definidos. Veja só:



Legal, se parece com uma senha super secreta ou algo assim.

Você pode estar se perguntando, o que este capítulo tem a ver com I/O? Nós não fizemos nada relacionado a I/O até agora. Bem, até agora nós sempre fizemos o nosso gerador de números aleatórios manualmente, fazendo-o com algum inteiro arbitrário. O problema é que se fizermos isso em nossos programas reais, eles irão sempre retornar os mesmos números aleatórios, o que definitivamente é nada bom para nós. É por isso que [code]System.Random[/code] oferece a ação de I/O [function]getStdGen[/function], que tem um tipo de [code]IO StdGen[/code]. Quando o programa é iniciado, ele pede ao sistema por um bom gerador de números aleatórios e armazena ele no chamado gerador global. [code]getStdGen[/code] obtém esse gerador aleatório global quando você o relaciona a algo.

Aqui está um simples programa que gera uma string aleatória.



Tenha cuidado, ao executar [code]getStdGen[/code] duas vezes ele vai pedir ao sistema para o mesmo gerador global duas vezes. Se você fizer isso:



você terá a mesma string impressa duas vezes! Uma maneira de obter duas strings diferentes de tamanho 20 é criando um fluxo infinito e em seguida, pegar os 20 primeiros caracteres e imprimi-los em uma linha e, logo a seguir, pegar os próximos 20 caracteres e imprimi-los na segunda linha. Para isso, podemos usar a função [code]splitAt[/code] de [code]Data.List[/code], que divide uma lista com um índice qualquer e retorna uma tupla que tem a primeira parte como sendo o primeiro componente e a segunda parte como sendo o segundo componente.



Outra maneira é usar a ação [function]newStdGen[/function], que divide o nosso atual gerador aleatório em dois geradores. Ele atualiza o gerador aleatório global com um deles e encapsula o outro como seu resultado.



Não obtemos apenas um novo gerador aleatório quando relacionamos [code]newStdGen[/code] há algo, o global também é atualizado, portanto se fizermos [code]getStdGen[/code] novamente e vinculá-lo a algo, teremos um gerador que não é o mesmo que [code]gen[/code].

Aqui está um pequeno programa que irá convidar o usuário a adivinhar qual número o programa está pensando.



Fazemos uma função [code]askForNumber[/code], que pega um gerador de números aleatórios e retorna uma ação I/O que irá solicitar ao usuário um número e dizer-lhe se ele adivinhou certo. Nesta função, primeiro geramos um número aleatório e um novo gerador baseado no gerador que obtemos como parâmetro e os chamamos de [code]randNumber[/code] e [code]newGen[/code]. Digamos que o número gerado foi [code]7[/code]. Então dizemos ao usuário para adivinhar qual o número que o programa está pensando. Executamos [code]getLine[/code] e associamos seu resultado a [code]numberString[/code]. Quando o usuário digita [code]7[/code], [code]numberString[/code] torna-se [code]"7"[/code]. Depois, usamos [code]when[/code] para verificar se a string digitada pelo usuário é uma string vazia. Se for, um ação I/O vazia de [code]return ()[/code] é executada, que efetivamente finaliza o programa. Se não for, a ação que existe no bloco <i>do</i> é executada. Usamos [code]read[/code] em [code]numberString[/code] para convertê-la em um número, assim [code]number[/code] agora é [code]7[/code].

<b>Desculpe-me!</b> Se o usuário nos der aqui alguma informação que [code]read[/code] não puder ler (como [code]"haha"[/code]), nosso programa irá quebrar com uma mensagem de erro bem feia. Se você não quer que seu programa falhe com entradas errôneas, use [function]reads[/function], que retorna uma lista vazia quando ele não consegue ler uma string. Quando ele consegue, ele retorna uma lista única com uma tupla que tem o nosso valor desejado como um componente e uma string com o que ele não consumiu como o outro.

Nós verificamos se o número que damos como entrada é igual ao gerado aleatoriamente e damos ao usuário a mensagem apropriada. E então chamamos [code]askForNumber[/code] recursivamente, só que desta vez com o novo gerador que obtemos, o que nos dá uma ação I/O que é igual à que foi realizada, ela apenas depende de um gerador diferente e então executamos isso.

[code]main[/code] consiste em apenas obter um gerador aleatório do sistema e chamar [code]askForNumber[/code] com ele para obter a ação inicial.

Aqui está o nosso programa em ação!



Outra maneira de fazer este mesmo programa seria assim:


É muito semelhante à versão anterior, só que em vez de fazer uma função que recebe um gerador e, em seguida, chama a si mesmo de forma recursiva com o novo gerador atualizado, nós fazemos todo o trabalho em [code]main[/code]. Depois de dizer ao usuário se eles estavam corretos em sua suposição ou não, nós atualizamos o gerador global e em seguida chamamos [code]main[/code] novamente. Ambas as abordagens são válidas, mas eu gosto mais da primeira, uma vez que faz menos coisas em [code]main[/code] e também nos fornece uma função que podemos reutilizar facilmente.
