Sintaxe de registro
===================

Certo, recebemos a tarefa de criar um tipo de dado que descreva uma pessoa. A informação que desejamos armazenar sobre a pessoa é: nome, sobrenome, idade, altura, número do telefone e sabor favorito de sorvete. Não sei você, mas isso é tudo que eu quero saber sobre uma pessoa. Vamos testar!


Certo. O primeiro campo é o nome, o segundo é o sobrenome, o terceiro é a idade e assim por diante. Vamos fazer uma pessoa.


Até que é legal, entretanto levemente ilegível. E se nós quisermos criar uma função que consiga informação separada da pessoa? Uma função que nos dá o nome da pessoa, uma função que nos dá o sobrenome da pessoa, etc. Bem, teríamos que defini-las mais ou menos dessa forma.


Vish! Eu certamente não curti escrever tudo isso! Apesar disso ser muito incômodo e ENTEDIANTE de se escrever, esse método funciona.


Deve existir um jeito melhor, você deve estar pensando! Bem, não, não há, desculpe.

Brincadeira, tem sim. Hahaha! Os criadores de Haskell eram muito espertos e anteciparam esse cenário. Eles incluíram uma forma alternativa de se escrever tipos de dados. Aqui está como nós poderíamos alcançar a funcionalidade mostrada acima utilizando sintaxe de registro.


Então ao invés de apenas nomear os tipos de campos um após o outro e separá-los com espaços, nós utilizamos chaves. Primeiro escrevemos o nome do campo, por exemplo, [code]firstName[/code], depois escrevemos dois pontos duplos [code]::[/code] (também chamados de Paamayim Nekudotayim, haha) e então especificamos seu tipo. O tipo de dado resultante é exatamente o mesmo. O principal benefício disso é que se cria funções que consultam os campos no tipo de dado. Ao utilizar sintaxe de registro para criar esse tipo de dado, Haskell automaticamente criou essas funções:
[code]firstName[/code], [code]lastName[/code], [code]age[/code], [code]height[/code], [code]phoneNumber[/code] and [code]flavor[/code].



Ainda há outro benefício ao se utilizar sintaxe de registro. Quando derivamos [code]Show[/code] para o tipo, ele se exibe diferentemente se utilizarmos sintaxe de registro para definir e instanciar o tipo. Digamos que nós temos um tipo que representa um carro. Queremos manter o registro da companhia que o fez, o nome do modelo e o ano de sua produção.
Observe.


Se nós o definirmos utilizando sintaxe de registro, podemos fazer um novo carro dessa maneira.


Quando estamos fazendo um novo carro, não temos que necessariamente colocar os campos na ordem exata, contanto que listemos todos eles. Mas se não utilizarmos sintaxe de registro, temos de especificá-los em ordem.

Utilize sintaxe de registro quando um construtor tiver vários campos e não for óbvio o que cada campo tem. Se fizermos um tipo de dado  de um vetor 3D escrevendo [code]data Vector = Vector Int Int Int[/code], é bem óbvio que os campos são componentes de um vetor. Entretanto, nos tipo de [code]Person[/code] e [code]Car[/code], não era tão óbvio e nós nos beneficiamos muito do uso de sintaxe de registro.
