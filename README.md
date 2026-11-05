# Silakka54 — Dvorak + acentos do português

Configuração do teclado split **Silakka54** (firmware Vial) com layout **Dvorak**, camadas de acentos, mouse e números. Inclui também o **layout de teclado do Linux** que faz os acentos funcionarem sem macro e sem Unicode.

```
vial/silakka54.vil        # layout do teclado (carregar no Vial)
xkb/symbols/usbr          # layout do sistema: US padrão + acentos em teclas extras
hypr/input.lua            # trecho do Hyprland que ativa o layout usbr
```

## Como funciona

O firmware Vial do Silakka54 não tem Unicode. Por isso os acentos são feitos assim:

1. O layout `usbr` (em `xkb/symbols/usbr`) é o **US padrão** com 12 teclas extras. Essas teclas existem no protocolo USB (são teclas de teclados japonês/coreano/ISO), mas não são usadas no dia a dia. Cada uma vira um caractere acentuado, com a maiúscula no Shift:

   | Caractere | Código no Vial | Tecla no xkb |
   |---|---|---|
   | á / Á | `KC_LANG1` | `<HNGL>` |
   | é / É | `KC_LANG2` | `<HJCV>` |
   | í / Í | `0x92` (LANG3) | `<KATA>` |
   | ó / Ó | `0x93` (LANG4) | `<HIRA>` |
   | ú / Ú | `KC_NONUS_BSLASH` | `<LSGT>` |
   | ç / Ç | `KC_KP_COMMA` | `<I129>` |
   | ã / Ã | `KC_RO` (INT1) | `<AB11>` |
   | â / Â | `KC_KANA` (INT2) | `<HKTG>` |
   | õ / Õ | `KC_JYEN` (INT3) | `<AE13>` |
   | ê / Ê | `KC_HENK` (INT4) | `<HENK>` |
   | ô / Ô | `KC_MHEN` (INT5) | `<MUHE>` |
   | à / À | `0x8c` (INT6) | `<JPCM>` |

2. No Vial, as camadas de acentos usam esses códigos.

O resto do teclado se comporta exatamente como o US padrão: aspas, til e circunflexo não viram teclas mortas, e o Alt direito continua sendo Alt.

> Isso só funciona em computadores com o layout `usbr` instalado. Em outra máquina, as teclas de acento não fazem nada (mas também não quebram nada).

## Instalação

### 1. Layout do sistema (xkb)

```bash
mkdir -p ~/.config/xkb/symbols
cp xkb/symbols/usbr ~/.config/xkb/symbols/usbr
```

O `libxkbcommon` procura layouts em `~/.config/xkb` automaticamente.

### 2. Hyprland (Omarchy)

Adicione o conteúdo de `hypr/input.lua` no final de `~/.config/hypr/input.lua`, depois:

```bash
hyprctl reload
```

```bash
hyprctl configerrors
```

```bash
omarchy restart xcompose
```

O último comando reinicia o fcitx5. Ele tem um teclado virtual próprio que precisa pegar o novo layout. Para conferir, todos os teclados devem aparecer como `English (US) + PT accents`:

```bash
hyprctl devices
```

### 3. Teclado (Vial)

No Vial: **File → Load saved layout** → `vial/silakka54.vil`.

## Camadas

Os diagramas mostram as teclas na ordem física, da esquerda para a direita.

### Camada 0 — base (Dvorak)

```
 `    1    2    3    4    5          6    7    8    9    0    =
Tab   '    ,    .    P    Y          F    G    C    R    L    /
Esc   A    O    E    U    I          D    H    T    N    S    -
Shift ;    Q    J    K    X          B    M    W    V    Z    \
               Alt Super Espaço    Enter Bksp Delete
```

Teclas com toque/segurar (Tap Dance, 175 ms):

| Tecla | Toque | Segurar | Toque duplo |
|---|---|---|---|
| Esc | Esc | Ctrl | — |
| Espaço | Espaço | camada 1 | — |
| Alt | acento minúsculo (camada 2) | Alt | acento maiúsculo (camada 3) |
| `\` | `\` | — | liga/desliga números (camada 5) |
| Delete | Delete | mouse (camada 4) | — |

### Camada 1 — navegação e F1–F12 (segurar Espaço)

- Linha de cima: F1–F12.
- Setas nas duas mãos, Home/End e `[` `]` na direita.
- Na coluna interna esquerda, de cima para baixo: macros de **Super+X**, **Super+C** e **Super+V** (recortar, copiar e colar universais do Omarchy).

### Camadas 2 e 3 — acentos (toque / toque duplo no Alt)

A camada vale para **uma tecla** e depois volta sozinha. A camada 2 tem as minúsculas e a camada 3 as mesmas posições em maiúsculas.

```
cima:   .   â   ô   ê
meio:   .   á   ó   é   ú   í
baixo:  .   ã   õ   ê   à
```

O **ç** fica na posição do **C** (mão direita).

### Camada 4 — mouse (segurar Delete)

Mão direita:

```
cima:   .        Rolar↑   Mouse↑   Rolar↓   .        .
meio:   Rolar←   Mouse←   Mouse↓   Mouse→   Rolar→   .
baixo:  .        .        Rápido   Médio    Lento    .
```

Mão esquerda:

- **U** = voltar (botão 4), **I** = avançar (botão 5)
- Dedão: **Espaço** = clique esquerdo, **Super** = clique direito, **Alt** = clique do meio

### Camada 5 — números (toque duplo no `\`)

Fica ligada até outro toque duplo no `\`. Mão direita:

```
cima:   /   7   8   9   -   ,
meio:   *   4   5   6   +   =
baixo:  0   1   2   3   .   \ (toque duplo = sair)
```

Usa os números da fileira de cima (não os do teclado numérico), então não depende do Num Lock.

## Cuidados com o tempo (Tap Dance)

O Tap Dance do Vial decide entre toque e segurar pelo tempo (175 ms). Se outra tecla for apertada antes disso, ele **sempre** entende como toque. Na prática:

- **Alt segurado:** para atalhos como Alt+Tab, segure o Alt ~175 ms antes da outra tecla. Se for rápido demais, entra na camada de acentos e a tecla sai sem Alt.
- **Toque duplo** (Alt para maiúsculas, `\` para números): depois do segundo toque, espere ~175 ms antes da próxima tecla.
- **`\`** sai com um pequeno atraso. Para digitar `\\`, dê os dois toques com uma pausa entre eles.
- **Delete** não repete ao segurar (segurar é o mouse). Para repetir, toque e logo em seguida segure.
