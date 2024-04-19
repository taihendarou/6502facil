---
layout: default
---

<h2 id="intro">Introdução</h2>

Neste pequeno e-book, vou mostrar a você como começar a escrever em linguagem de 
montagem 6502. O processador 6502 foi um grande sucesso nos anos setenta e 
oitenta, alimentando computadores famosos como o 
[BBC Micro](http://en.wikipedia.org/wiki/BBC_Micro), 
[Atari 2600](http://en.wikipedia.org/wiki/Atari_2600), 
[Commodore 64](http://en.wikipedia.org/wiki/Commodore_64),
[Apple II](http://en.wikipedia.org/wiki/Apple_II) e o [Nintendo Entertainment 
System](http://en.wikipedia.org/wiki/Nintendo_Entertainment_System). Bender, 
em Futurama, [tem um processador 6502 como cérebro](http://www.transbyte.org/SID/SID-files/Bender_6502.jpg). 
[Até o Terminator foi programado em 6502](http://www.pagetable.com/docs/terminator/00-37-23.jpg).

Então, por que você iria querer aprender 6502? É uma língua morta, não é? Bem, o latim também é. E ainda assim, eles continuam ensinando isso. [Q.E.D.](http://en.wikipedia.org/wiki/Q.E.D.)

(Na verdade, fui informado de maneira confiável que os processadores 6502 ainda estão sendo produzidos pelo [Western Design Center](http://www.westerndesigncenter.com/wdc/w65c02s-chip.cfm) e [vendidos para hobbistas](http://www.mouser.co.uk/Search/Refine.aspx?Keyword=65C02), então claramente 6502 *não* é uma linguagem morta! Quem diria?)

Mas falando sério, acho que é valioso ter um entendimento da linguagem de montagem. A linguagem de montagem é o nível mais baixo de abstração em computadores - o ponto em que o código ainda é legível. A linguagem de montagem se traduz diretamente nos bytes que são executados pelo processador do seu computador. Se você entender como isso funciona, basicamente se tornou um [mágico](http://skilldrick.co.uk/2011/04/magic-in-software-development/) da computação.

Então, por que 6502? Por que não uma linguagem de montagem *útil*, como [x86](http://en.wikipedia.org/wiki/X86)? Bem, eu não acho que aprender x86 seja útil. Não acho que você terá que *escrever* linguagem de montagem no seu trabalho diário - isso é puramente um exercício acadêmico, algo para expandir sua mente e seu pensamento. O 6502 foi originalmente escrito em uma época diferente, um tempo em que a maioria dos desenvolvedores estava escrevendo assembly diretamente, em vez de nessas novas linguagens de programação de alto nível. Então, foi projetado para ser escrito por humanos. As linguagens de montagem mais modernas são feitas para serem escritas por compiladores, então vamos deixar isso para eles. Além disso, 6502 é *divertido*. Ninguém nunca chamou x86 de *divertido*.


<h2 id="first-program">Nosso primeiro programa</h2>

Então, vamos começar! Abaixo temos um pequeno assembler e [simulador de processador 6502 feito em JavaScript](https://github.com/skilldrick/6502js) que foi adaptado para este material.
Clique em **Assemble** e então em **Run** para montar e executar o pedaço de código em linguagem assembly.

{% include start.html %}
LDA #$01
STA $0200
LDA #$05
STA $0201
LDA #$08
STA $0202
{% include end.html %}

Espero que a área preta à direita agora tenha três "pixels" coloridos no canto superior esquerdo. (Se isso não funcionar, você provavelmente precisará atualizar seu navegador para algo mais moderno, como Chrome ou Firefox.)

Então, o que esse programa está fazendo, de fato? Vamos acompanhá-lo com o depurador. Clique em **Reset**, depois marque a caixa **Debugger** para iniciar o depurador. Clique em **Step** uma vez. Se você estava observando com atenção, terá notado que `A=` mudou de `$00` para `$01`, e `PC=` mudou de `$0600` para `$0602`.

Qualquer número prefixado com `$` na linguagem de montagem 6502 (e, por extensão, neste livro) está no formato hexadecimal (hex). Se você não está familiarizado com números hexadecimais, recomendo que leia [o artigo da Wikipedia](http://en.wikipedia.org/wiki/Hexadecimal). Qualquer coisa prefixada com `#` é um valor numérico literal. Qualquer outro número se refere a um local de memória.

Equipado com esse conhecimento, você deve ser capaz de perceber que a instrução `LDA #$01` carrega o valor hexadecimal `$01` no registrador `A`. Entrarei em mais detalhes sobre registradores na próxima seção.

Pressione **Step** novamente para executar a segunda instrução. O pixel no canto superior esquerdo do display do simulador agora deve estar branco. Este simulador usa os locais de memória de `$0200` a `$05ff` para desenhar pixels em seu display. Os valores de `$00` a `$0f` representam 16 cores diferentes (`$00` é preto e `$01` é branco), então armazenar o valor `$01` no local de memória `$0200` desenha um pixel branco no canto superior esquerdo. Isso é mais simples do que como um computador real emitiria vídeo, mas serve por agora.

Então, a instrução `STA $0200` armazena o valor do registrador `A` no local de memória `$0200`. Clique em **Step** mais quatro vezes para executar o restante das instruções, mantendo um olho no registrador `A` conforme ele muda.

### Exercícios ###

1. Tente trocar a cor dos três pixels.
2. Mude a posição de um dos pixels, para que ele seja exibido na lateral inferior direita (endereço de memória `$05ff`).
3. Insira novas instruções para exibir mais pixels.


<h2 id='registers'>Registradores e flags</h2>

Nós já mensionamos levemente a área de status do processador (os bits em
`A`, `PC` etc.), mas o que isso significa de fato?

A primeira linha mostra os registradores `A`, `X` e `Y` (`A` é chamado de "acumulador"). Cada registrador armazena um único byte. A maioria das operações matemáticas do processador serão realizadas 
com o contéudo desses registradores.

`SP` significa stack pointer. Não vamos entrar neste assunto agora, mas, basicamente, este é um registrador que decrementa cada vez que um byte é empurrado para a pilha (stack) e que incrementa sempre que um byte é retirado da pilha.

`PC` significa program counter - é como o processador sabe em que ponto o programa está atualmente. É como se fosse a linha atual que está sendo executada em um script. Neste simulador em JavaScript, o código é montado a partir do endereço de memória `$6000`, então o `PC` sempre inicia com esse valor.

A última sessão se refere as flags do processador. Cada flag é um bit, então as sete flags consistem em um único byte. As flags são acionadas pelo processador para nos dar informações sobre a instrução que acabou de ser executada. Falaremos mais sobre isso mais tarde. [Read more
about the registers and flags here](https://web.archive.org/web/20210626024532/http://www.obelisk.me.uk/6502/registers.html).


<h2 id='instructions'>Intruções</h2>

Instruções, em linguagem assembly, são como pequenos conjuntos de funções pré-definidas. As instruções podem ou não conter também um argumento. Veja a seguir um código comentado para introduzir algumas instruções:

{% include start.html %}
LDA #$c0  ;Carrega o valor hexadecimal $c0 no registrador A
TAX       ;Transfere o valor de A para o registrador X
INX       ;Incrementa o valor do registrador X
ADC #$c4  ;Adiciona o valor hexadecimal $c4 ao valor já presente no registrador A
BRK       ;Interrompe a execução do programa
{% include end.html %}

Monte o código (assembly), depois ative o depurador (debugger) e siga pelo código, instrução por instrução, observando 
os registradores `A` e `X`. Talvez você note algo após a linha `ADC #$c4`, pois ao adicionarmos `$c4` a `$c0`, esperaríamos pelo resultado `$184`, mas o processador 
acaba apresentando o resultado como `$84`. Por que isso acontece?

O problema é que `$184` é grande demais para caber em um único byte (o máximo é `$FF`), e os registradores deste processador só podem conter um único byte. 
No entanto, esta reação já foi prevista pelos desenvolvedores. Se você observar atentamente, 
notará que a flag carry foi definida como `1` após esta operação.

Experimente **digitar** (não copie e cole) o código a seguir no simulador:

    LDA #$80
    STA $01
    ADC $01

{% include widget.html %}

Uma coisa importante a se notar aqui é a distinção entre `ADC #$01` e `ADC $01`. 
O primeiro adiciona o valor `$01` ao registrador `A`, mas o segundo adiciona o 
valor armazenado no endereço de memória `$01` ao registrador `A`.

Monte o programa (assembly), marque a opção **Monitor**, e então navegue por estas 
três instruções. O monitor mostrará uma seção da memória, e pode ser útil para 
visualizar o que acontece durante a execução de programas. `STA $01` armazena o 
valor do registrador `A` no local de memória `$01`, e `ADC $01` adiciona o valor 
armazenado no local de memória `$01` ao registrador `A`. `$80 + $80` deveria ser 
igual a `$100`, mas como esse valor estrapola o limite de um byte, o registrador 
`A` é definido como `$00` e a flag de carry é ativada. Além disso, a flag de 
zero também é ativada. A flag Zero sempre será ativada quando o resultado de uma 
instrução for zero.

Uma lista completa do conjunto de instruções 6502 está [disponível aqui](http://www.6502.org/tutorials/6502opcodes.html) 
e [aqui](http://www.obelisk.me.uk/6502/reference.html) (Eu geralmente me refiro a ambas as páginas, pois elas têm seus 
pontos fortes e fracos). Essas páginas detalham os argumentos de cada instrução, quais registradores elas usam, e quais flags elas definem.

### Exercícios ###

1. Já vimos a instrução `TAX`. Então, você pode supor o que `TAY`, `TXA` e `TYA` fazem. Escreva um código com elas para confirmar se é isso mesmo.
2. Reescreva o primeiro exemplo desta sessão, mas utilize o registrador `Y` ao invés do registrador `X`
3. O oposto de `ADC` (adicionar com carry / add with carry) é `SBC` (subtrair com carry / subtract with carry). Escreva um programa que use essa instrução.

<h2 id='branching'>Ramificações (Branching)</h2>

Até agora, só conseguimos escrever programas básicos sem qualquer lógica de ramificação, loop ou dinâmica para transitar por diferentes regiões do código. Todas as instruções foram executadas da primeira até a última, em sequência. Chegou a hora de mudar isso!

A linguagem assembly para 6502 possui várias instruções de ramificação, todas baseadas em se certas flags estão definidas ou não. Neste exemplo, vamos olhar para `BNE`: "Branch on not equal" (Ramificar se não igual).

{% include start.html %}
  LDX #$08
decremento:
  DEX
  STX $0200
  CPX #$03
  BNE decremento
  STX $0201
  BRK
{% include end.html %}

Primeiro carregamos o valor `$08` no registrador `X`. A próxima linha é um rótulo.
Rótulos apenas marcam certos pontos em um programa para que possamos retornar a eles mais tarde. Você identificará um ródulo pela presença de um `:` após a expressão.
Após o rótulo, decrementamos `X`, armazenamos em `$0200` (o pixel superior esquerdo) e então comparamos ao valor `$03`.
[`CPX`](http://www.obelisk.me.uk/6502/reference.html#CPX) compara o valor no registrador `X` com outro valor. Se os dois valores forem iguais, a flag `Z` é definida como `1`, caso contrário, é definida como `0`.

A próxima linha, `BNE decremento`, deslocará a execução do programa para o rótulo de decremento se a flag `Z` for `0` (significando que os dois valores na comparação `CPX` não eram iguais), caso contrário, não faz nada e armazenamos `X` em `$0201`, então terminamos o programa.

Em assembly, geralmente você usará rótulos com instruções de ramificação. Quando montado, no entanto, este rótulo é convertido em um deslocamento relativo de um único byte (um número de bytes para ir para trás ou para frente da próxima instrução) então instruções de ramificação só podem ir para frente e para trás em torno de 256 bytes (ou, em hexadecimal, de #$00 a #$FF). Isso significa que eles só podem ser usados para mover pela região próxima do código. Para saltos mais distantes, usaremos as instruções de salto, que serão ensinadas mais a frente.

### Exercícios ###

1. O oposto de `BNE` é `BEQ`. Tente escrever um programa que use `BEQ`.
2. `BCC` e `BCS` ("branch on carry clear" e "branch on carry set") são usados para ramificar com base na flag de Carry. Escreva um programa que use uma dessas duas.
3. (Extra) Faça um resumo com todas as instruções apresentadas até agora e escreva, com suas palavras, a função de cada uma delas.

<h2 id='addressing'>Modos de endereçamento (Addressing modes)</h2>

O 6502 usa um barramento de endereço de 16 bits, 
o que significa que há 65536 bytes de memória 
disponíveis para o processador. Lembre-se de que 
um byte é representado por dois caracteres hexadecimais, 
então os endereços de memória geralmente são representados 
como `$0000 - $ffff`. Existem várias maneiras de se 
referir a esses endereços de memória, conforme detalharemos 
a seguir.

Com todos esses exemplos, recomendo que use o monitor de memória 
para observar em detalhes as mudanças de valores nos endereços 
de memória durante a execução dos códigos. O monitor requer um 
endereço de memória inicial e um número de bytes para exibir a partir 
desse endereço. Ambos são valores hexadecimais. Por exemplo, para 
exibir 16 bytes de memória de `$c000`, insira `c000` e `10` em **Início** e **Comprimento**, respectivamente.

### Absolute: `$c000` ###

Com o endereçamento absoluto, o local completo da memória é usado como argumento para a instrução. Por exemplo:

    STA $c000 ;Armazena o valor no acumulador no local de memória $c000
    
### Página Zero (Zero page) : `$c0` ###

Todas as instruções que suportam endereçamento absoluto (com exceção das instruções de salto) também têm a opção de usar um endereço de um único byte. Esse tipo de endereçamento é chamado de "página zero" - apenas a primeira página (os primeiros 256 bytes) da memória é acessível. Isso é mais rápido, pois apenas um byte precisa ser consultado, e também ocupa menos espaço no código montado.

Página Zero é uma área especial de memória nos primeiros 256 bytes do espaço de endereço de 
um computador que usa o processador 6502. Devido à sua localização especial (de $0000 a $00FF),
acessar dados na Página Zero é mais rápido e requer menos instruções, tornando o código mais 
eficiente.

A Página Zero é comumente usada para armazenar variáveis e ponteiros que são frequentemente 
acessados ou modificados, devido à sua rápida acessibilidade e eficiência em termos de espaço de código. Ela é ideal para operações críticas de desempenho, como loops de processamento gráfico, rotinas
de manipulação de dados intensivos e funções que exigem acesso rápido e eficiente à memória.

Os bytes armazenados na página zero podem ser utilizados para situações como contagem de 
looks armazenamento e cálculo de ponteiros registros temporários variáveis de jogo de estado 
controle de Flex buffet de entrada e saída e outras características que aprenderemos mais 
à frente

### Zero page,X: `$c0,X` ###

É aqui que o endereçamento se torna interessante. Neste modo, é fornecido um endereço de 
página zero, e então o valor do registrador `X` é adicionado. Aqui está um exemplo:

    LDX #$01   ;Atribui $01 ao registrador X
    LDA #$aa   ;Atribui $aa ao registrador A
    STA $a0,X  ;Armazena o valor de A no local de memória $a1
    INX        ;Incrementa X
    STA $a0,X  ;Armazena o valor de A no local de memória $a2

Acompanhe o que ocorre neste conjunto de instruções, linha por linha, observando os 
valores presentes nos registradores e ns endereço sde memória usados. Assim, aprenderá 
bem o funcionamento destas instruções.

Outro exemplo:

    LDX #$05
    STA $ff,X ;Armazena o valor de A no local de memória $04
	
Para compreender as instruções acima, lembre-se que #$FF é o valor imediatamente antes do #$00.

### Zero page,Y: `$c0,Y` ###

Este é similar ao zero page,X, mas pode ser usado apenas com `LDX` and `STX`.

### Absolute,X e absolute,Y: `$c000,X` e `$c000,Y` ###

Estas são a versão com endereçamento absoluto de zero page,X e zero page, Y. Por exemplo:

    LDX #$01
    STA $0200,X ;Armazena o valor de A no endereço de memória $0201

Diferentemente de zero page,Y, absolute,Y não pode ser usado com `STX`, mas pode ser usado 
com `LDA` e `STA`.

### Imediato: `#$c0` ###

O endereçamento imediato não se restringe a endereços de memória - este é o modo onde valores 
absolutos são utilizados. Por exemplo, `LDX #$01` carrega o valor `$01` no registrador `X`, o 
que é completamente diferente da instrução de zero page `LDX $01`, que carrega o valor presente 
no endereço de memória `$01` no registrador `X`.

### Relativo: `$c0` (ou rótulo) ###

Endereçamento relativo é usado para instruções de ramificação (branch). Estas instruções ocupam 
um único byte, que é utilizado como um offset a partir da instrução que vem em seguida.

Monte o código a seguir e então clique em **Hexdump** para ver o código em hexadecimal.

{% include start.html %}
  LDA #$01
  CMP #$02
  BNE nao_igual
  STA $22
nao_igual:
  BRK
{% include end.html %}

O código em hexadecimal deverá ser algo como:

    a9 01 c9 02 d0 02 85 22 00

`a9` e `c9` são os opcodes do processador para endereçamento-imediato `LDA` e `CMP`,
respectivamente. `01` e `02` são os argumentos para estas instruções. `d0` é
o opcode para `BNE`, sendo `02` o seu argumento. Isto significa "pule os próximos 
dois bytes" (`85 22`, a versão em linguagem de máquina de `STA $22`). Tente editar o 
código para que `STA` utilize um endereço absoluto de dois bytes em vez de um endereço 
de página zero (zero page) de um único byte (por exemplo, altere `STA $22` para `STA $2222`). 
Remonte o código e olhe para o hexdump novamente - o argumento para `BNE` agora deve ser `03`, 
porque a instrução que o processador está pulando agora tem três bytes de comprimento.

### Implícito ###

Algumas instruções não necessitam de endereços de memória como argumento, como por exemplo `INX`, incrementa o valor presente no registrador `X`. Estas instruções possuem o que chamamos de endereçamento implícito. Ou seja, o argumento está implícito na instrução.

### Indireto: `($c000)` ###

O endereçamento indireto consiste em usar um endereço absoluto para buscar por outro endereço. O valor presente no endereço passado como argumento da instrução será o byte menos significativo do endereço final. O byte logo após ele será o byte mais significativo do endereço final. Isto ficará mais claro após você ver alguns exemplos:

{% include start.html %}
LDA #$01
STA $f0
LDA #$cc
STA $f1
JMP ($00f0) ;referência para $cc01
{% include end.html %}

Neste exemplo, `$f0` contém o valor `$01`, enquanto `$f1` contém o valor `$cc`. A instrução `JMP ($f0)` fará com que o processador busque os dois bytes presentes em `$f0` e `$f1` (que são `$01` e `$cc`) e junte-os para formar o endereço `$cc01`, que é para onde de fato o programa fará o salto. Monte este programa e analise linha por linha para ver o que acontece. Falaremos mais sobre `JMP` na sessão referente aos [saltos](#jumping).

### Indexação indireta: `($c0,X)` ###

Pode parecer um pouco estranho, mas é como uma mistura entre zero page,X e endereçamento indireto. Basicamente, pegaremos um endereço da página zero e somaremos o valor presente no registrador `X`, para então usá-lo para buscar por um endereço de dois bytes. Por exemplo:

{% include start.html %}
LDX #$01
LDA #$05
STA $01
LDA #$07
STA $02
LDY #$0a
STY $0705
LDA ($00,X)
{% include end.html %}

Os endereços de memória `$01` e `$02` possuem, respectivamente, os valores `$05` e `$07`. Considere `($00,X)` como se fosse `($00 + X)`. Neste exemplo, `X` está com o valor de `$01`, então simplificamos para `($01)`. A partir daqui, continuamos como o endereçamento indireto padrão - os dois bytes em `$01` e `$02` (`$05` e `$07`) são buscados para formar o endereço `$0705`. Este é o endereço que o registrador `Y` tinha armazenado na instrução anterior, portanto o registrador `A` receberá o mesmo valor que `Y`.


### Indexado indiretamente: `($c0),Y` ###

O modo indexado indiretamente é semelhante ao indireto indexado, mas é menos complicado. Diferentemente de adicionar o registrador `X` ao endereço *antes* de acessá-lo, neste caso, primeiro acessamos o endereço na página zero. Em seguida, adicionamos o valor do registrador `Y` ao endereço que foi acessado. Confira o exemplo:

{% include start.html %}
LDY #$01
LDA #$03
STA $01
LDA #$07
STA $02
LDX #$0a
STX $0704
LDA ($01),Y
{% include end.html %}

Neste caso, `($01)` busca pelos dois bytes em `$01` e `$02`: `$03` e `$07`. Assim teremos o endereço `$0703`. O valor do registrador `Y` será adicionado a este endereço para chegarmos ao endereço final `$0704`.

### Exercício ###

1. Escreva pequenos códigos usando cada um dos modos de endereçamento do 6502. Lembre-se que você pode usar o monitor daqui do simulador para acompanhar os valores de um bloco de memória.


<h2 id='stack'>A pilha (stack)</h2>

A pilha (stack) do processador 6502 é como qualquer outra pilha - valores são inseridos (push) e retirados (pull). Ou seja, valores são "empilhados". A profundidade atual da pilha é indicada por um registrador especial chamado *stack pointer* (que podemos traduzir como *ponteiro de pilha*). A pilha fica presente na memória, nos endereços entre `$0100` e `$01ff`. O ponteiro de pilhas tem `$ff` como valor inicial, que aponta para o endereço de memória `$01ff`. Quando um byte é inserido na pilha, o valor do ponteiro de pilha muda para `$fe`, ou endereço de memória `$01fe`, e assim por diante.

Duas instruções usadas para manipular a pilha são `PHA` e `PLA`, "push accumulator" (adiciona o valor do acumulador à pilha) e "pull accumulator" (retira um valor da pilha e coloca no acumulador). Veja um exemplo prático destas duas instruções:

{% include start.html %}
  LDX #$00
  LDY #$00
primeiro_loop:
  TXA
  STA $0200,Y
  PHA
  INX
  INY
  CPY #$10
  BNE primeiro_loop ;fica em loop até que Y seja $10
segundo_loop:
  PLA
  STA $0200,Y
  INY
  CPY #$20      ;fica em loop até que Y seja $20
  BNE segundo_loop
{% include end.html %}

`X` armazena a cor, enquanto `Y` armazena a posição do pixel. O primeiro loop desenha a cor atual como um pixel (através do registrador `A`), insere o valor na pilha e então incrementa a cor e a posição. O segundo loop retira um byte da pilha, desenha essa cor na tela como um pixel e então incrementa a posição. O resultado esperado disso é um padrão espelhado.


<h2 id='jumping'>Saltos (Jumping)</h2>

Saltos (Jumping) assemelham-se a ramificações, mas apresentam duas distinções importantes. A primeira é que os saltos são executados de maneira incondicional, ou seja, sempre ocorrem, independentemente de condições prévias. A segunda é que o salto sempre direciona para um endereço de dois bytes. Esta característica tem pouca relevância em programas menores, pois você utilizará rótulos (labels) e o assembler se encarregará de ajustar o endereço de memória correto a partir desses rótulos. No entanto, em programas maiores, os saltos são essenciais para transitar entre diferentes seções do código.

### JMP ###

A instrução `JMP` representa um salto incondicional. Veja um exemplo bem simples para ver esta instrução em ação:

{% include start.html %}
  LDA #$03
  JMP destino
  BRK
  BRK
  BRK
destino:
  STA $0200
{% include end.html %}


### JSR/RTS ###

`JSR` e `RTS`, que significam "jump to subroutine" (salto para sub-rotina) e "return from subroutine" (retornar da sub-rotina), são duas instruções que você comumente verá sendo utilizadas juntas. `JSR` é usada para saltar de uma parte do código para outra. `RTS` retorna para a o local do salto anterior. Se você está familiarizado com linguagens de mais alto nível, isto seria como executar uma função e então retornar.

O processador sabe para onde retornar porque `JSR` armazena o ponteiro atual na pilha antes de realizar o salto. `RTS` retira este endereço da pilha e salta de volta para o ponto de partida. Veja o exemplo:

{% include start.html %}
  JSR inicio
  JSR loop
  JSR fim

inicio:
  LDX #$00
  RTS

loop:
  INX
  CPX #$05
  BNE loop
  RTS

fim:
  BRK
{% include end.html %}

A primeira instrução faz com que a execução execute com salto para o rótulo `inicio`. Lá, o programa atribuirá um valor para o registrador `X` e então a execução do programa retornará para a instrução logo abaixo `JSR inicio`, que é `JSR loop`. Isto significa que agora será realizado um salto para a instrução com o rótulo `loop`. Lá, o valor de `X` será incrementado em 1 até que seja igual a `$05`. Depois, o programa retornará e a instrução `JSR fim` será executada, a qual realizará um salto para o fim do programa. Este exemplo ilustra como `JSR` e `RTS` podem ser utilizados em conjunto para modularizar o seu código, semelhante ao que é feito em linguagens de alto nível como Python e C#.


<h2 id='snake'>Criando um jogo</h2>

Agora, vamos colocar todo esse conhecimento em prática e criar um jogo! Vamos desenvolver uma versão bem simples do clássico jogo da cobrinha, 'Snake'.

Embora esta seja uma versão simplificada, o código será consideravelmente maior do que todos os exemplos anteriores. Precisaremos monitorar vários endereços de memória simultaneamente para os diferentes aspectos do jogo.

Neste assembler, assim como na maioria dos disponíveis para programas efetivos, podemos definir constantes (ou símbolos) que representam números. Assim, poderemos usar no código as constantes ao invés de valores numéricos literais. Podemos usar letras, números e underline nos nomes das constantes.

Veja um exemplo. Note que os operandos imediatos ainda necessitam do prefixo `#`.
{% include start.html %}
  define  sysRandom  $fe ; um endereço
  define  a_dozen    $0c ; uma constante
 
  LDA sysRandom  ; equivalente a "LDA $fe"

  LDX #a_dozen   ; equivalente a "LDX #$0c"
{% include end.html %}

O widget do simulador logo abaixo contém todo o código fonte do jogo. Vamos explicar como ele funciona nas sessões subsequentes.

[Willem van der Jagt](https://twitter.com/wkjagt) fez um [gist totalmente comentado deste código](https://gist.github.com/wkjagt/9043907), acesse para mais detalhes (em inglês).

{% include snake.html %}


### Estrutura geral ###

Depois do bloco com comentários (linha iniciando com ponto e vírgula), as duas primeiras linhas são:

    jsr init
    jsr loop

`init` e `loop` são duas sub-rotinas. `init` configura o status inicial do jogo e `loop` é o loop principal do jogo.

A sub-rotina de `loop` chamará por outras sub-rotinas, sequencialmente, e então voltará para o início dela mesma.

    loop:
      jsr readkeys
      jsr checkCollision
      jsr updateSnake
      jsr drawApple
      jsr drawSnake
      jsr spinwheels
      jmp loop

Primeiro, `readkeys` verifica se uma das teclas direcionais (W, A, S, D) foi pressionada e, se sim, ajusta a direção da cobrinha de acordo. Em seguida, `checkCollision` confirma se houve colisão da cobrinha com ela mesma ou com a maçã. `updateSnake` atualiza a representação interna da cobrinha conforme sua nova direção. Depois, tanto a maçã quanto a cobrinha são desenhadas na tela. Por fim, `spinWheels` faz o processador realizar tarefas desnecessárias para retardar a execução do jogo, funcionando como um comando de pausa. O jogo continua até que a cobrinha colida com a parede ou consigo mesma.


### Página Zero: `$c0` ###

A página zero da memória é utilizada para armazenar diversas variáveis de estado do jogo, conforme indicado no bloco de comentários no topo do código. Todos os endereços em `$00`, `$01` e de `$10` para cima correspondem a pares de bytes que representam uma localização de memória de dois bytes, a qual será acessada usando endereçamento indireto. Essas localizações de memória estarão todas entre `$0200` e `$05ff`, que é a seção da memória correspondente ao display do simulador. Por exemplo, se `$00` e `$01` contiverem os valores `$01` e `$02`, eles estariam indicando o segundo pixel do display (`$0201` - lembre-se, o byte menos significativo é o primeiro no endereçamento indireto).

Os primeiros dois bytes armazenam a localização da maçã. Eles são atualizados sempre que a cobrinha come a maçã. O byte `$02` contém a direção atual. `1` significa cima, `2` significa direita, `4` baixo, e `8` significa esquerda. O motivo da escola destes números ficará mais claro depois.

Por fim, o byte `$03` contém o comprimento atual da cobrinha, em termos de bytes na memória (então um comprimento de 4 equivale a 2 pixels).

### Inicialização ###

A sub-rotina `init` desmembra para duas outras sub-rotinas, `initSnake` e `generateApplePosition`. `initSnake` define a direção e o comprimento da cobrinha, e então carrega os endereços iniciais de memória da cabeça e do corpo da cobrinha. O par de bytes em `$10` contém a localização na tela da cabeça, o par em `$12` contém a localização do único segmento do corpo, e `$14` contém a localização da cauda (a cauda é o último segmento do corpo e é desenhada em preto para manter a cobrinha em movimento). Isso ocorre no seguinte código:


    lda #$11
    sta $10
    lda #$10
    sta $12
    lda #$0f
    sta $14
    lda #$04
    sta $11
    sta $13
    sta $15

Isto carregará o valor `$11` no endereço de memória `$10`, o valor `$10` em `$12`, e `$0f` em `$14`. Então, carrega o valor `$04` em `$11`, `$13` e `$15`. Isso deixará a memória como a seguir:

    0010: 11 04 10 04 0f 04

que representa os endereços de memória indiretos `$0411`, `$0410` e `$040f` (três pixels no meio da tela). Estou insistindo neste ponto, mas é importante compreender completamente como funciona o endereçamento indireto.

A próxima sub-rotina, `generateApplePosition`, configura o endereço de memória da maçã para uma posição aleatória na tela. Primeiro, carrega um byte aleatório no acumulador (`$fe` é um gerador de números aleatórios neste simulador). Isso é armazenado em `$00`. Em seguida, um byte aleatório diferente é carregado no acumulador, que então é combinado usando a operação `AND` com o valor `$03`. Esta parte requer um pequeno desvio.

O valor hexadecimal `$03` é representado em binário como `00000011`. O opcode `AND` performance uma operação bitwise AND entre o argumento e o acumulador. Por exemplo, se o acumulador contém o valor binário `10101010`, então o resultado do `AND` com `00000011` será `00000010`.

O efeito disto é que estaremos criando uma máscara dos bits menos significativos do acumulador, ficando estes com o valor um e os demais com o valor de zero. Essa operação converterá um número no intervalo de 0&ndash;255 para um número no intervalo de 0&ndash;3.

Depois disso o valor `2` será adicionado ao acumulador, para criar um último valor aleatório no intervalo de 2&ndash;5

O resultado desta sub rotina para carregar com um byte aleatório no endereço `$00` e um número entre 2 a 5 no endereço `$01`. Considerando que o bike menos significante vem primeiro no endereçamento indireto, isto nos leva a um endereço de memória que varia entre `$0200` e `$05ff`: O exato intervalo de endereços usados para o display deste simulador.


### O loop de jogo ###

Praticamente todos os jogos possuem um loop de jogo em seu núcleo. Todos os loops de jogos seguem a mesma estrutura básica: recebem uma entrada do usuário, atualizam o estado do jogo e renderizam esse estado. Aqui, nosso loop fará exatamente isso.


#### Leitura de comandos do jogador ####

A primeira sub-rotina, `readKeys`, será responsável por aceitar os comandos inseridos pelo jogador. Neste simulador, o endereço de memória `$ff` armazena o código ascii da última tecla pressionada. O valor será carregado no acumulador e então comparado com `$77` (código hexadecimal para W), `$64 (hexadecimal para D), `$73` (hexadecimal para S) e `$61` (hexadecimal para A). Se alguma destas comparações for válida, o programa moverá a execução para a sessão apropriada. Cada sessão (`upKey`, `rightKey` etc) irá primeiro verificar se a direção atual da cobrinha é o oposto da nova direção e isso gerará um novo desvio.

Como mencionado, as quatro direções são representadas internamente pelos números 1, 2, 4 e 8. Todos estes números são potência de 2 e foram escolhidos porque cada um deles, em sistema binário, possuem um único dígito `1`, conforme podemos ver seguir:

    1 => 0001 (cima)
    2 => 0010 (direita)
    4 => 0100 (baixo)
    8 => 1000 (esquerda)

O opcode `BIT` é semelhante ao `AND`, mas sua execução é utilizada apenas para ajustar a flag zero - o resultado efetivo da operação é descartado. A flag zero é definida somente se o resultado da operação de AND entre o acumulador e o argumento resultar em zero. Quando lidamos com potências de dois, a flag zero só será definida se os dois números forem diferentes. Por exemplo, `0001 AND 0001` não resulta em zero, enquanto `0001 AND 0010` resulta em zero.

Sendo assim, considerando a rotina `upKey`, se a direção atual da cobrinha for para baixo (4), o teste de bit resultará em zero. `BNE` significa "branch if the zero flag is clear" (ramificar se a flag zero estiver desativada), então neste caso ocorrerá uma ramificação para `illegalMove`, que fará com que retornemos da sub-rotina atual. Caso a direção atual não seja para baixo (4), a nova direção (para cima) da cobrinha (1) será armazenada no endereço de memória apropriado. O mesmo princípio é aplicado nas demais direções.

#### Atualizando o estado de jogo ####

A próxima sub-rotina, `checkCollision`, delega para `checkAppleCollision` e `checkSnakeCollision`. `checkAppleCollision` apenas verifica se os dois bytes que armazenam a localização da maçã coincidem com os dois bytes que armazenam a localização da cabeça. Se coincidirem, o comprimento é aumentado e uma nova posição para a maçã é gerada.

`checkSnakeCollision` percorre os segmentos do corpo da cobrinha, comparando cada par de bytes com o par da cabeça. Se houver uma coincidência de valores, significa que houve uma colisão, então o jogo termina.

Depois de verificarmos se houve uma colisão, atualizamos a localização da cobrinha. Isso é feito da seguinte forma: Primeiro, movemos cada par de bytes do corpo, um por um, uma posição para cima na memória. Depois, atualizamos a cabeça de acordo com a direção atual. Por fim, se a cabeça estiver fora dos limites, consideramos que houve uma colusão. Vamos ilustrar isso com uma arte em ASCII. Considere que cada par de colchetes contém uma coordenada x e y (ao invés de um par de bytes), para simplificar.

      0    1    2    3    4
    Cabeça              Cauda
    
    [1,5][1,4][1,3][1,2][2,2]    Posição inicial

    [1,5][1,4][1,3][1,2][1,2]    Valor de (3) é copiado para (4)
    
    [1,5][1,4][1,3][1,3][1,2]    Valor de (2) é copiado para (3)
    
    [1,5][1,4][1,4][1,3][1,2]    Valor de (1) é copiado para (2)
    
    [1,5][1,5][1,4][1,3][1,2]    Valor de (0) é copiado para (1)
    
    [0,5][1,5][1,4][1,3][1,2]    Valor de (0) é atualizado de acordo com a direção

Quando trabalhamos em linguagem de baixo nível, realizar este tipo de verificação é um pouco mais complexo do que a maneira que foi explicado. Primeiro, o comprimento será carregado no registrador `X`, que então será decrementado. O bloco abaixo mostra a memória inicial para a cobrinha.

    Endereço de memória:   $10 $11 $12 $13 $14 $15

    Valor:                 $11 $04 $10 $04 $0f $04

A largura é inicialmente definida como `4`, logo, o valor de `X` começa em `3`. A instrução `LDA $10,x` carrega o valor localizado em `$13` no registrador `A`. Em seguida, `STA $12,X` armazena esse valor em `$15`. Após isso, `X` é decrementado e o loop se inicia. Com `X` agora em `2`, o valor em `$12` é carregado e armazenado no endereço `$14`. Este loop prosseguirá enquanto `X` for positivo, onde `BPL` representa "branch if positive" (ramificar se positivo).

Uma vez que os valores foram deslocados ao longo da cobrinha, precisamos decidir o que fazer com a cabeça. Primeiro, a direção é carregada em `A`. `LSR` significa "logical shift right" (deslocamento lógico para a direita), ou seja, "desloca todos os bits uma posição para a direita". O bit menos significativo é deslocado para a flag de carry, então, se o acumulador está em `1`, após o `LSR`, ele vai para `0`, com a flag de carry ativada.

Para verificar se a direção é `1`, `2`, `4` ou `8`, o código realiza deslocamentos sucessivos para a direita até que a flag de carry seja ativada. Um deslocamento `LSR` indica "cima", dois deslocamentos indicam "direita" e assim sucessivamente.

O próximo bit atualiza a cabeça da cobrinha de acordo com a direção. Esta é possivelmente a parte mais complicada do código e tudo depende de como os endereços de memória são mapeados na tela. Sendo assim, vamos analisar com mais detalhes.

Você pode pensar na tela como quatro faixas horizontais de 32 × 8 pixels. Essas faixas estão mapeadas em `$0200-$02ff`, `$0300-$03ff`, `$0400-$04ff` e `$0500-$05ff`. As primeiras linhas de pixels são `$0200-$021f`, `$0220-$023f`, `$0240-$025f`, etc.

As long as you're moving within one of these horizontal strips, things are
simple. For example, to move right, just increment the least significant byte
(e.g. `$0200` becomes `$0201`). To go down, add `$20` (e.g. `$0200` becomes
`$0220`). Left and up are the reverse.

Going between sections is more complicated, as we have to take into account the
most significant byte as well. For example, going down from `$02e1` should lead
to `$0301`. Luckily, this is fairly easy to accomplish. Adding `$20` to `$e1`
results in `$01` and sets the carry bit. If the carry bit was set, we know we
also need to increment the most significant byte.

After a move in each direction, we also need to check to see if the head
would become out of bounds. This is handled differently for each direction. For
left and right, we can check to see if the head has effectively "wrapped
around". Going right from `$021f` by incrementing the least significant byte
would lead to `$0220`, but this is actually jumping from the last pixel of the
first row to the first pixel of the second row. So, every time we move right,
we need to check if the new least significant byte is a multiple of `$20`. This
is done using a bit check against the mask `$1f`. Hopefully the illustration
below will show you how masking out the lowest 5 bits reveals whether a number
is a multiple of `$20` or not.

    $20: 0010 0000
    $40: 0100 0000
    $60: 0110 0000

    $1f: 0001 1111

I won't explain in depth how each of the directions work, but the above
explanation should give you enough to work it out with a bit of study.


#### Renderizando o jogo ####

Because the game state is stored in terms of pixel locations, rendering the
game is very straightforward. The first subroutine, `drawApple`, is extremely
simple. It sets `Y` to zero, loads a random colour into the accumulator, then
stores this value into `($00),y`. `$00` is where the location of the apple is
stored, so `($00),y` dereferences to this memory location. Read the "Indirect
indexed" section in [Addressing modes](#addressing) for more details.

Next comes `drawSnake`. This is pretty simple too - we first undraw the tail
and then draw the head. `X` is set to the length of the snake, so we can index
to the right pixel, and we set `A` to zero then perform the write using the
indexed indirect addressing mode. Then we reload `X` to index to the head, set
`A` to one and store it at `($10,x)`. `$10` stores the two-byte location of
the head, so this draws a white pixel at the current head position. As only
the head and the tail of the snake move, this is enough to keep the snake
moving.

The last subroutine, `spinWheels`, is just there because the game would run too
fast otherwise. All `spinWheels` does is count `X` down from zero until it hits
zero again. The first `dex` wraps, making `X` `#$ff`.
