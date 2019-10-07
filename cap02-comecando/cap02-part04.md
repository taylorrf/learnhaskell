Texas ranges
============

E se precisar de uma lista com todos os números entre 1 e 20? É só digitar todos eles, porém obviamente esta não seria uma solução para quem deseja a excelência em sua linguagem de programação. Em função disso, utilizamos ranges. Ranges é um jeito de construir listas que são uma sequência aritmética de elementos devidamente enumerados. Números podem ser enumerados. Um, dois, três, quatro, etc. Caracteres também podem ser enumerados. O alfabeto é uma enumeração de caractéres de A a Z. Nomes também podem ser enumerados. O que vem depois de "João"? Não faço idéia.

Para constituir uma lista que contenha todos os números naturais de 1 a 20, você simplesmente deve escrever [code][1..20][/code]. Isto é o equivalente a ter escrito [code][1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20][/code] e não há diferença entre um e outro com a exceção de que escrever longas sequências enumeradas manualmente é algo bastante estúpido.

Ranges também são legais porque você pode definir uma etapa específica. E se quisermos todos os números ímpares entre 1 e 20? Ou se quisermos sempre ter o terceiro número entre 1 e 20?

Esta é uma questão simples de separar os primeiros dois elementos com uma vírgula e então especificar qual o limite máximo. Embora bastante esperto, ranges com diversas etapas não são tão espertos como muitas pessoas esperam que eles sejam. Você não pode fazer [code][1,2,4,8,16..100][/code]  e esperar que isso lhe retorne a potência de 2. Primeiramente porque você pode especificar somente uma etapa. E segundo que algumas sequências não são aritméticas, mas sim ambíguas se tivermos somente alguns dos primeiros termos.

Para ter uma lista com todos os números entre 20 e 1, você não pode simplesmente fazer [code][20..1][/code], você deve fazer [code][20,19..1][/code].

Veja como utilizamos números ponto flutuante em ranges! Como eles não são completamente precisos (por definição), o uso deles em ranges pode retornar alguns resultados que cheiram bem mal.

Meu conselho é que não se faça uso deles em ranges de listas.

Você também pode utilizar ranges para fazer listas infinitas, basta não especificar qual o limite máximo. Depois iremos adentrar com mais detalhes nas listas infinitas. No momento, vamos examinar como podemos ter os primeiros 24 múltiplos de 13. Claro, você quer fazer [code][13,26..24*13][/code]. Porém eis aqui um jeito melhor: [code]take 24 [13,26..][/code]. Como Haskell é preguiçoso, ele não vai tentar analisar uma lista infinita imediatamente porque isto nunca vai acabar. Ele vai ficar esperando para ver no que esta lista infinita vai dar. E aqui ele vê que você quer apenas os primeiros 24 elementos e de bom grado lhe retorna eles.

Algumas funções para produzirmos listas infinitas:

[code]cycle[/code] recebe uma lista e gera ciclos infinitos dela. Se tentar mostrar o resultado, continuará para sempre até que você tente cortá-lo fora em algum lugar.

[code]repeat[/code] recebe um elemento e produz uma lista infinita dele. Isto é como o ciclo de uma lista com somente um elemento.

No entanto será mais simples utilizar a função [code]replicate[/code] caso você deseje algumas repetições do mesmo elemento em uma lista. [code]replicate 3 10[/code] retornará [code][10,10,10][/code].
