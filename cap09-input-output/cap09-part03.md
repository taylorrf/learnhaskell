Argumentos de linha de comando
==============================

Lidar com argumentos de linha de comando é quase uma necessidade se você quiser criar um script ou aplicação que rode em um terminal. Felizmente, a biblioteca padrão de Haskell tem um método elegante de obter os argumentos de linha de comando de um programa.

Na seção anterior, fizemos um programa para adicionar um item à nossa lista de tarefas a fazer e um programa para remover um item. Existem dois problemas com a nossa abordagem. O primeiro é que simplesmente embutimos o nome do  arquivo contendo a lista no nosso código. Nós simplesmente decidimos que o arquivo se chamaria <i>todo.txt</i> e que o usuário nunca teria a necessidade de lidar com várias listas de coisas a fazer.

Um modo de resolver isso é sempre perguntar ao usuário qual arquivo ele quer usar como sua lista. Usamos essa abordagem quando queríamos saber qual item o usuário queria excluir. Isso funciona, mas não é tão bom, porque requer que o usuário execute o programa, espere que o programa pergunte algo e então responda a ele. Isso é chamado de programa interativo e a dificuldade com programas de linha de comando interativos é a seguinte &mdash; e se você quiser automatizar a execução desse programa, como feito em um script de lotes? É mais difícil criar um script que interaja com um programa do que um script que simplesmente invoque um programa ou vários deles.

Por isso que algumas vezes é melhor que o usuário diga ao programa o que ele quer quando ele executar o programa, ao invés de o programa perguntar ao usuário quando for executado. E que melhor maneira de o usuário dizer ao programa o que ele quer que seja feito do que usando argumentos de linha de comando.

O módulo <span class="fixed">System.Environment</span> tem duas ações de I/O interessantes. Uma é [function]getArgs[/function], que tem o tipo <span class="fixed">getArgs :: IO [String]</span> e é uma ação de I/O que receberá os agumentos com os quais o programa foi invocado e terá como resultado encapsulado uma lista com os argumentos. [function]getProgName[/function] tem o tipo <span class="fixed">getProgName :: IO String</span> e é uma ação de I/O que contém o nome do programa.

Aqui está um pequeno programa que demonstra como as duas funcionam:

Nós associamos <span class="fixed">getArgs</span> e <span class="fixed">progName</span> a <span class="fixed">args</span> e <span class="fixed">progName</span>. Escrevemos <span class="fixed">Os argumentos são:</span> e então para todo argumento em <span class="fixed">args</span>, executamos <span class="fixed">putStrLn</span>. Finalmente, também imprimimos o nome do programa. Vamos compilar isto como <span class="fixed">arg-test</span>.

Perfeito. Munido desse conhecimento você poderia criar algumas aplicações legais de linha de comando. Na verdade, vamos prosseguir e criar uma. Na seção anterior, fizemos um programa para adicionar tarefas e outro para excluí-las. Agora, iremos juntá-los em um único programa, que irá funcionar dependendo dos argumentos de linha de comando. Também o faremos operar com diferentes arquivos, não somente com <i>todo.txt</i>.

Iremos chamá-lo simplesmtente de <i>todo</i> e ele será capaz de fazer três coisas diferentes:

    <li>Visualizar tarefas</li>
    <li>Adicionar tarefas</li>
    <li>Excluir tarefas</li>

Por enquanto não iremos nos preocupar muito com possíveis entradas erradas.

Nosso programa será feito de uma forma que se quisermos adicionar a tarefa <span class="fixed">Encontrar a espada mágica do poder</span> ao arquivo <i>todo.txt</i>, teremos que digitar <span class="fixed">todo add todo.txt "Encontrar a espada mágica do poder"</span> no terminal. Para visualizar as tarefas simplesmente escreveremos <span class="fixed">todo view todo.txt</span> e para remover a tarefa com o índice 2, escreveremos <span class="fixed">todo remove todo.txt 2</span>.

Vamos começar criando uma lista de associações de redirecionamento. Ela consiste em uma lista de associações simples que tem argumentos de linha de comando como chaves e funções como seus valores correspondentes. Todas essas funções serão do tipo <span class="fixed">[String] -&gt; IO ()</span>. Elas irão receber a lista de argumentos como um parâmetro e retornar uma ação de I/O que fará a vizualização, adição, remoção, etc.

Temos ainda que definir <span class="fixed">main</span>, <span class="fixed">add</span>, <span class="fixed">view</span> e <span class="fixed">remove</span>, então vamos começar com <span class="fixed">main</span>:

Primeiro, pegamos os argumentos e os associamos a <span class="fixed">(command:args)</span>. Se você se lembra de casamento de padrões, isso significa que o primeiro argumento será associado a <span class="fixed">command</span> e o resto deles será associado a <span class="fixed">args</span>. Se executarmos nosso programa usando a linha de comando <span class="fixed">todo add todo.txt "Espancar o macaco"</span>, <span class="fixed">command</span> será <span class="fixed">"add"</span> e <span class="fixed">args</span> será <span class="fixed">["todo.txt", "Espancar o macaco"]</span>.

Na próxima linha, procuramos nosso comando na lista de redirecionamentos. Como <span class="fixed">"add"</span> está associado a <span class="fixed">add</span>, obtemos <span class="fixed">Just add</span> como resultado. Usamos casamento de padrão novamente para extrair nossa função do <span class="fixed">Maybe</span>. O que acontece se nosso comando não estiver na lista de redirecionamento? Bem, então a busca retornará <span class="fixed">Nothing</span>, mas dissemos que não nos preocuparemos muito em falhar elegantemente, então o casamento de padrões falhará e nosso programa lançará uma exceção.

Finalmente, chamamos nossa função <span class="fixed">action</span> (que agora é, digamos, <span class="fixed">add</span>) com o restante da lista de argumentos. Isso retornará uma ação de I/O que adiciona um item, mostra uma lista de itens ou remove um item e devido a essa ação ser parte do bloco <i>do</i> <span class="fixed">main</span>, ela será executada.

Excelente! Tudo que falta agora é implementar <span class="fixed">add</span>, <span class="fixed">view</span> e <span class="fixed">remove</span>. Vamos começar com <span class="fixed">add</span>:

Se chamarmos nosso programa com a linha de comando <span class="fixed">todo add todo.txt "Espancar o macaco"</span>, o <span class="fixed">"add"</span> será associado a <span class="fixed">command</span> no primeiro casamento de padrões no bloco <span class="fixed">main</span>, enquanto <span class="fixed">["todo.txt", "Espancar o macaco"]</span> será passada à função que nós obtemos da lista de redirecionamento. Então, como não estamos lidando com entradas incorretas por enquanto, nós apenas reconhecemos imediatamente um padrão que é uma lista com esses dois elementos e retornamos uma ação de I/O que acrescenta a linha ao final do arquivo, juntamente com um caractere de nova linha.

Agora, vamos implementar a funcionalidade de visualização da lista. Se quisermos visualizar os itens em um arquivo, escrevemos <span class="fixed">todo view todo.txt</span>. Assim, no primeiro casamento de padrão, <span class="fixed">command</span> será <span class="fixed">"view"</span> e <span class="fixed">args</span> será <span class="fixed">["todo.txt"]</span>.

Nós já fizemos praticamente a mesma coisa no programa que apenas remove tarefas quando estamos mostrando as tarefas para que o usuário possa escolher uma para remoção, só que aqui nós apenas mostramos as tarefas.

Por fim, vamos implementar <span class="fixed">remove</span>. Ela será bem parecida com o programa que apenas remove tarefas, então se você não entender como a remoção de itens funciona aqui, dê uma olhada na explicação sobre aquele programa. A principal diferença é que não estamos embutindo <i>todo.txt</i> no código, mas obtendo com um argumento. Nós também não estamos perguntando ao usuário o número da tarefa a remover, estamos obtendo ele como um argumento.

Nós abrimos o arquivo com o nome <span class="fixed">fileName</span> e abrimos um arquivo temporário, removemos a linha com o índice que o usuário deseja excluir, escrevemos isso no arquivo temporário, removemos o arquivo original e renomeamos o arquivo temporário para <span class="fixed">fileName</span>.

Aqui está o programa todo de uma vez, em toda sua glória!

Para resumir nossa solução: fizemos uma associação de redirecionamento que mapeia comandos para funções que recebem alguns argumentos de linha de comando e retornam uma ação de I/O. Nós vemos o que é o comando e obtemos a função apropriada através da lista de redirecionamento. Chamamos essa função com o restante dos argumentos da linha de comando para obter uma ação de I/O que fará o procedimento apropriado e então simplesmente executamos essa ação!

Em outras linguagens, poderíamos ter implementado isso com um grande bloco switch ou algo parecido, mas usar funções de alta ordem nos permite simplesmente dizer à lista de redirecionamento para nos dar a função apropriada e então dizer a essa função para nos dar uma ação de I/O para alguns argumentos de linha de comando.

Vamos testar nossa aplicação!

Outra coisa legal sobre isto é que é fácil adicionar funcionalidades adicionais. Basta adicionar uma entrada na lista de associações de redirecionamento e implementar a função correspondente e não terá nada mais para se preocupar! como um exercício, você pode tentar implementar uma função <span class="fixed">bump</span> que irá receber um arquivo e um número de tarefa e retornar uma ação de I/O que coloca essa tarefa no topo da lista de coisas a  fazer.

Você poderia fazer esse programa falhar de um modo um pouco mais elegante no caso de más entradas (por exemplo, se alguém escrever <span class="fixed">todo UP YOURS HAHAHAHA</span>) criando uma ação de I/O que somente notifique que houve um erro (digamos, <span class="fixed">errorExit :: IO ()</span>), depois verificando possíveis entradas inconsistentes e se existirem, executa-se a ação de I/O de notificação de erros. Outro modo é usar exceções, que nós estudaremos em breve.