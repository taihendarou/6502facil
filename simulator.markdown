---
layout: basic
---

<h2>Simulador</h2>

Para usar o disassembler, clique em **Assemble**, e depois **Disassemble**. [Voltar para 6502 Fácil](index.html).

{% include start.html %}
start:
  jsr init

loop:
  jsr drawMap
  jsr genMap
  jmp loop

testMemory:
  rts
  pha
  txa
  pha

  lda #0
  ldx $10
  sta $500,x
  ldx $78
  lda #1
  sta $500,x
  stx $10

  lda #0
  ldx $11
  sta $500,x
  ldx $79
  lda #3
  sta $500,x
  stx $11

  lda #0
  ldx $12
  sta $500,x
  ldx $7a
  lda #4
  sta $500,x
  stx $12

  lda #0
  ldx $13
  sta $500,x
  ldx $7b
  lda #4
  sta $500,x
  stx $13

  pla
  tax
  pla

  rts

init:
  ldx #0
  lda walls

;desenha os 256 pixels da barede no topo e na base
drawinitialwalls:
  sta $200,x ;desenha o topo da parede
  sta $400,x ;desenha a base da parede
  dex        ;contagem decrescente a partir de 00
  cpx #0     ;até atingirmos 0 novamente (256 ciclos
  bne drawinitialwalls

  lda #$10
  sta $80
  ldx #$0f

;preencher $81-$90 com $10 (offset inicial da parede)
setinitialwalloffsets:
  sta $81,x  ; algo
  dex
  bpl setinitialwalloffsets
  rts

;--

drawMap:
  lda #$00
  sta $78
  lda #$20
  sta $79
  lda #$c0
  sta $7a
  lda #$e0
  sta $7b

  ldx #$0f
drawLoop:
  lda $81,x
  sta $82,x ;deslocar offset da parede junto

  tay
  sty $02      ;armazena offset atual da parede em $02
  lda pixels,y ;busca offset atual da parede em pixels
  sta $00      ;e armazena em $00 
  iny
  lda pixels,y ;busca offset atual da parede + 1 pixel
  sta $01      ;e armazena em $01
               ;$00 agora aponta para um endereço de memória de 2 bytes

  lda walls
  ldy $78      ;limite superior da parede
  sta ($00),y
  iny
  sta ($00),y

  ldy $7b
  sta ($00),y ;limite inferior da parede
  iny
  sta ($00),y

  ldy $79     ;limite superior do túnel
  lda #0      ;preto para o túnel
  sta ($00),y
  iny
  sta ($00),y

  ldy $7a
  sta ($00),y ;limite inferior do túnel
  iny
  sta ($00),y

  ; mover offset dois pixels para a direita
  inc $78
  inc $79
  inc $7a
  inc $7b
  inc $78
  inc $79
  inc $7a
  inc $7b
  dex
  bpl drawLoop
  rts

;---

genMap:
  lda $80 ;$80 é o ponto de inflexão da próxima parede
  cmp $81 ;$81 é o offset da próxima parede
  beq newinflectionpoint
  lda $80
  clc
  sbc $81 ;o próximo deslocamento da parede está acima ou abaixo do ponto de inflexão?
  bpl raisewalls
  bmi lowerwalls
newinflectionpoint:
  lda $fe
  and #$f ;transforma em 4-bits
  asl     ;duplicar (transforma em número par)
  sta $80 ;define $80 para um valor aleatório
  rts
lowerwalls:
  dec $81
  dec $81
  rts
raisewalls:
  inc $81
  inc $81
  rts

pixels:
  dcb $00,$02,$20,$02,$40,$02,$60,$02
  dcb $80,$02,$a0,$02,$c0,$02,$e0,$02
  dcb $00,$03,$20,$03,$40,$03,$60,$03
  dcb $80,$03,$a0,$03,$c0,$03,$e0,$03

walls:
  dcb $d
{% include end.html %}
