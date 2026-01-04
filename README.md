# Pac-Man para PEPE-16 (contador de segundos + 2º fantasma)

## Menbros do Grupo
* Constantino Manuel Domingos Gola
* Nicklânder Kiakuenda Amândio Metusal Bueia
* João Miguel Francisco
* Gelson Cabuco

Implementação de um mini **Pac-Man em Assembly para o simulador PEPE-16**, com:
- **Pac-Man 3x3** controlado pelo teclado
- **Fantasma 1** perseguindo o Pac-Man (movimento “lento” via timer)
- **Fantasma 2** (novo) que **ativa após alguns segundos** e também persegue
- **Objetos nos 4 cantos** (colecionáveis) + condição de vitória
- **Vidas** e **Game Over**
- **Contador de segundos** exibido nos **displays hex** (formato decimal 00–99)
- **Bordas vermelhas** usando uma segunda camada de pixels

---

## 📌 Requisitos

- Simulador **PEPE-16 / AC2025** (ou ambiente compatível)
- Montador/assembler do PEPE-16
- Mapa de memória e periféricos conforme o enunciado do simulador

---

## 🧠 Como o projeto está organizado

O código é um único ficheiro `.asm` (ou equivalente) e está dividido em blocos:

### 1) Constantes e endereços importantes
- `BUFFER (4000H)`: memória para guardar a tecla
- `PIN (0E000H)`: porto de entrada do teclado
- `POUT (0C000H)`: porto de saída do teclado
- `pixelsMatriz (8000H)`: camada principal do ecrã
- `pixelsVermelho (9000H)`: camada vermelha (bordas)
- `displays (0A000H)`: displays hexadecimais

### 2) Variáveis do jogo
- Posição do Pac-Man: `linha_pac`, `coluna_pac`
- Fantasma 1: `fantasma_linha`, `fantasma_coluna`, `fantasma_timer`
- Fantasma 2: `fantasma2_linha`, `fantasma2_coluna`, `fantasma2_ativa`, `fantasma2_timer`
- Estado do jogo: `vidas`, `pontuacao`, `game_active`
- Objetos: `objeto_0..objeto_3`, `objetos_coletados`
- Contador: `segundo_atual`, `contador_decimal`

### 3) Sprites 3x3
- `sprite_pacman`: formato “C”
- `sprite_fantasma`: formato “X”
- `sprite_canto`: formato “+”
- `sprite_caixa`: quadrado (caixa central)

### 4) Funções principais (rotinas)
- **Desenho e pixels:** `pixel_xy`, `apagar_pixel_xy`, `desenhar_sprite_3x3`, `apagar_sprite_3x3`
- **Pac-Man:** `mover_pac_cima/baixo/esquerda/direita`, `desenhar_pacman`
- **Fantasmas:** `mover_fantasma_lento`, `mover_fantasma`, `mover_fantasma2_lento`, `mover_fantasma2`, `desenhar_fantasma`, `desenhar_fantasma2`
- **Colisões:** `verificar_colisao`, `verificar_colisao2`
- **Objetos/Vitória:** `desenhar_cantos`, `verificar_colisao_objetos`, `verificar_vitoria_global`, `vitoria`
- **Game Over:** `perder_vida`, `mostrar_game_over`
- **Tempo/Display:** `atualizar_contador_segundos`, `mostrar_contador_display`
- **Bordas vermelhas:** `limpar_camada_vermelha`, `desenhar_bordas_vermelhas`, `desenhar_pixel_vermelho`
- **Teclado:** `pTeclado`
- **Setup:** `Carregamento`

---

## 🎮 Controles

Teclas (teclado matricial do PEPE-16):
- **1** → mover **CIMA**
- **9** → mover **BAIXO**
- **4** → mover **ESQUERDA**
- **6** → mover **DIREITA**
- **F** → terminar (desativa o jogo e mostra Game Over)

> Observação: a leitura do teclado é feita por varredura usando `POUT` e leitura em `PIN`.

---

## 🕹️ Regras do jogo

### ✅ Objetivo
Coletar os **4 objetos dos cantos** (sprites “+”) nas posições aproximadas:
- (2,2), (2,27), (27,2), (27,27)

Quando `objetos_coletados == 4` → chama `vitoria` e termina o jogo.

### 👻 Fantasmas
- **Fantasma 1** começa ativo e se move com atraso (timer).
- **Fantasma 2** começa **inativo** e é ativado após alguns segundos, passando a perseguir o Pac-Man.

### 💥 Colisão
Se Pac-Man colidir com qualquer fantasma:
- chama `perder_vida`
- decrementa `vidas`
- reposiciona Pac-Man
- se `vidas == 0` → Game Over

### ⏱️ Contador
- Incrementa um contador interno e, ao atingir o valor configurado (ex.: 60 ciclos), considera “1 segundo”.
- Mostra nos displays em **decimal 00–99** via conversão para “BCD” manual (dezenas/unidades).

---

## 🧪 Como compilar e executar

1. Crie um ficheiro, por exemplo:
   - `pacman_pepe16.asm`
2. Abra no **simulador PEPE-16/AC2025**.
3. Monte/compile o código (Assembler do ambiente).
4. Execute (Run).
5. Use as teclas **1/9/4/6** para mover.

> Dica: se o jogo parecer muito rápido/lento, ajuste o valor do `delay` e/ou o comparador do “1 segundo” em `atualizar_contador_segundos` (ex.: `MOV R3, 60`).

---

## 🔧 Configurações úteis

### Ajustar velocidade do jogo
- `delay` controla o “frame-rate”
- `fantasma_timer` / `fantasma2_timer` controlam a frequência de movimento dos fantasmas

### Ajustar tempo de ativação do Fantasma 2
A rotina `ativar_segundo_fantasma` usa `contador_decimal` para decidir quando ativar.
- Para ativar após **N segundos**, compare `contador_decimal` com `N`.

---

## 🐞 Notas e cuidados

- O ecrã é tratado como **matriz de bits** (pixels), usando `ptable` para selecionar o bit correto.
- As posições (linha/coluna) assumem limites típicos (0 a 28) para não “sair do ecrã”.
- O `game_active` controla se o loop principal continua a atualizar o jogo.

---

## ✅ Checklist rápido

- [x] Pac-Man move com 1/9/4/6  
- [x] Fantasma 1 persegue e colide  
- [x] Fantasma 2 ativa depois e persegue  
- [x] Objetos dos cantos desaparecem ao coletar  
- [x] Vitória ao coletar 4 objetos  
- [x] Contador aparece nos displays  
- [x] Bordas vermelhas visíveis  

---

## 📄 Licença
Uso académico/educacional. Ajuste conforme o teu enunciado/professor exija.

---

## Autor
Projeto desenvolvido por: **(coloca teu nome aqui)**  
Curso/Unidade Curricular: **(coloca aqui)**  
Ano: **2025/2026**
