Tipos paramétricos
==================

Um construtor de valor pode pegar alguns parâmetros de valores e então produzir um novo valor. Por exemplo, o construtor [code]Car[/code] pega três valores e produz um valor de carro. De maneira similar, <em> construtores de tipo</em> podem pegar tipos como parâmetros e produzir novos tipos. Isso pode parecer um pouco meta à primeira vista, mas não é muito complicado. Se você tem familiaridade com os templates de C++, você verá alguns pontos paralelos. Para se ter uma imagem clara de como os parâmetros de tipos funcionam na prática, vamos dar uma olhada em como um tipo que você já encontrou é implementado.


O [code]a[/code] aqui é um parâmetro de tipo. E por haver um parâmetro de tipo envolvido, chamamos [code]Maybe[/code] um construtor de tipo. Dependendo do que quisermos que esse tipo de dado guarde quando não for [code]Nothing[/code], esse construtor de tipo pode acabar produzindo um tipo [code]Maybe Int[/code], [code]Maybe Car[/code], [code]Maybe String[/code], etc. Nenhum valor pode ter um tipo de apenas [code]Maybe[/code], isso porque ele não é um tipo por si só, é um construtor de tipo. Para que isso se torne um tipo real do qual um valor pode ser parte, ele tem que ter todos os seus parâmetros preenchidos.


Então se passarmos [code]Char[/code] como o parâmetro de tipo para [code]Maybe[/code], obtemos o tipo de [code]Maybe Char[/code]. O valor [code]Just 'a'[/code] tem um tipo de [code]Maybe Char[/code], por exemplo.


Você pode não saber disso, mas nós usamos um tipo que tem um parâmetro de tipo antes de usar [code]Maybe[/code]. Estamos falando do tipo lista. Apesar de haver algum açúcar sintático na jogada, o tipo lista pega um parâmetro para produzir um tipo concreto. Valores podem ter um tipo [code][Int][/code], um tipo [code][Char][/code], um tipo [code]String[/code], mas você não pode ter um valor que tenha um tipo de apenas [code][][/code].


Vamos brincar um pouco com o tipo [code]Maybe[/code]


Parâmetros de tipo são úteis porque podemos criar diferentes tipos com eles dependendo de que espécie de tipos desejamos ter guardados no nosso tipo de dado. Quando fazemos [code]:t Just "haha"[/code], o mecanismo de inferência de tipo descobre que isso é do tipo [code]Maybe [Char][/code], porque se o [code]a[/code] no código [code]Just a[/code] for uma String, então o [code]a[/code] em [code]Maybe a[/code] também deve ser uma String.


Perceba que o tipo de [code]Nothing[/code] é [code]Maybe a[/code]. Seu tipo é polimórfico. Se alguma função precisar de um [code]Maybe Int[/code] como parâmetro, podemos passar um Nothing, porque [code]Nothing[/code] não contém um valor, logo não importa. O tipo [code]Maybe a[/code] pode agir como um [code]Maybe Int[/code] se ele tiver de fazê-lo, assim como [code]5[/code] pode agir como um [code]Int[/code] ou um [code]Double[/code]. Similarmente, o tipo da lista vazia é [code][a][/code]. Uma lista vazia pode agir como um lista de qualquer coisa. Por isso que podemos fazer [code][1, 2, 3] ++ [][/code] e [code][!ha", "ha", "ha"] ++ [][/code].


Usar parâmetros de tipos é muito benéfico, mas apenas quando usá-los faz sentido. Geralmente nós usamos quando nossos tipos de dados pudessem funcionar, independentemente do tipo do valor que está guardado dentro de si, como no nosso tipo [code]Maybe a[/code]. Se nosso tipo se comportar como uma espécie de caixa, isso será bom para o utilizarmos. Poderíamos mudar nosso tipo de dado [code]Car[/code] assim:


Para isso:


Mas haveria algum benefício? A resposta é: provavelmente não, uma vez que acabaríamos apenas definindo funções que apenas funcionariam com o tipo [code]Car String String Int[/code]. Por exemplo, dada nossa primeira definição de [code]Car[/code], poderíamos criar uma função que exibe as propriedades do carro em um texto pequeno e legal.



Uma função pequenina e fofinha! A declaração de tipo é fofa e funciona bem. E se [code]Car[/code] fosse [code]Car a b c[/code]?



Teríamos de forçar essa função a usar o tipo de [code]Car[/code] de [code](Show a) =&gt; Car String String a[/code]. Você pode ver que a assinatura de tipo é mais complicada e o único benefício que nós obteríamos seria que agora podemos usar qualquer tipo que seja uma instância da typeclass [code]Show[/code] como o tipo para [code]c[/code].



Entretanto na vida real, nós acabaríamos usando [code]Car String String Int[/code] na maioria das vezes e então pareceria que parametrizar o tipo [code]Car[/code] não vale à pena. Nós geralmente utilizamos parâmetros de tipos quando o tipo que está contido dentro dos vários construtores de valor de tipos de dados não é realmente importante para o funcionamento do tipo. Uma lista de coisas é uma lista de coisas e não importa o tipo dessas coisas, a lista ainda funciona. Se quisermos somar uma lista de números, podemos especificar depois como a função de soma funciona especificamente para lista de números. O mesmo serve para [code]Maybe[/code]. [code]Maybe[/code] representa uma opção de tanto não se ter nada como se ter alguma coisa. Não importa o tipo dessa coisa.


Outro exemplo de um tipo parametrizado que já encontramos por aí é [code]Map k v[/code] de [code]Data.Map[/code]. O [code]k[/code] é o tipo das chaves em um mapa e [code]v[/code] é o tipo dos valores. Esse é um bom exemplo de onde os parâmetros são muito úteis. Ter mapas parametrizados nos permite ter mapeamentos de qualquer tipo para qualquer outro tipo, contanto que o tipo da chave seja parte da typeclass [code]Ord[/code]. Se estivéssemos definindo um tipo mapeador, poderíamos adicionar uma restrição de typeclass na declaração <i>data</i>:



Entretanto, existe uma convenção muito forte em Haskell para <em>nunca adicionar restrições de typeclass na declaração de dados. </em>. Por quê? Bem, porque não há muitos benefícios, mas acabamos escrevendo mais restrições de classe, até quando não precisamos delas. Se colocarmos ou não a restrição[code]Ord k[/code] na declaração <i>data</i> para [code]Map k v[/code], teríamos de colocar uma restrição nas funções que assumem que as chaves de um mapa podem ser ordenadas. Mas se não colocarmos a restrição na declaração do dado, não teremos de colocar [code]Ord k[/code] na declaração de tipos de função que não se importam se k pode ser ordenado ou não. Um exemplo de tal função é [code]toList[/code], que apenas pega um mapeamento e converte-o em uma lista associativa. Sua assinatura de tipo é [code]toList :: Map k a -&gt; [(k, a)][/code]. Se [code]Map k v[/code] tivesse uma restrição de tipo na sua declaração <i>data</i>, o tpo para [code]toList[/code] teria de ser [code]toList :: (Ord k) =&gt; Map k a -&gt; [(k, a)][/code], mesmo que a função não fizesse nenhuma comparação de ordem entre as chaves.

Então não coloque restrições de tipo em declarações <i>data</i> mesmo se parecer fazer sentido, porque você terá de colocá-las nas declarações de função de qualquer forma.

Vamos implementar um tipo de vetor 3D e adicionar algumas operações para ele. Usaremos um tipo parametrizado porque mesmo sabendo que, em geral, o vetor conterá números, ainda vale a pena dar suporte a vários deles.



[code]vplus[/code] é para adicionar dois vetores. Dois vetores são adicionados apenas somando seus respectivos componentes.[code]scalarMult[/code] é para o produto escalar entre dois vetores e [code]vectMult[/code] é para multiplicar um vetor por um escalar. Essas funções podem operar nos tipos de [code]Vector Int[/code], [code]Vector Integer[/code], [code]Vector Float[/code], qualquer coisa, contanto que [code]a[/code] de [code]Vector a[/code] seja da typeclas [code]Num[/code]. Além disso, se você examinar a declaração de tipo dessas funções, você verá que elas apenas podem operar em vetores do mesmo tipo e os números envolvidos devem ser do mesmo tipo que está contido dentro dos vetores. Perceba que não colocamos uma restrição de classe [code]Num[/code] na declaração <i>data</i>, uma vez que teríamos que repeti-la nas funções de qualquer forma.

Mais uma vez, é importante distinguir entre o construtor de tipo e o construtor de valor. Quando estamos declarando um tipo de dado, a parte antes do [code]=[/code] é o construtor de tipo e os construtores depois dele (possivelmente separados por [code]|[/code]'s) são construtores de valor. Dar um tipo [code]Vector t t t -&gt; Vector t t t -&gt; t[/code] à uma função seria errado porque temos que colocar os tipos na declaração de tipo e o construtor de tipo vetor recebe apenas um parâmetro, enquanto que o construtor de valor recebe três. Vamos brincar com nossos vetores.
