# Roteiro Completo — TP02: Trajetória de um Robô
**Universidade Católica de Brasília — Estrutura de Dados | 1º Semestre de 2026**

---

## Etapa 1 — Entender o que você vai construir

Antes de escrever qualquer linha de código, fixe isso na cabeça:

- Um **robô** começa em uma posição `(x, y)` e recebe uma sequência de comandos (`'N'`, `'S'`, `'L'`, `'O'`).
- Você vai usar **listas encadeadas** (sem vetores!) para guardar os comandos e as posições visitadas.
- Você implementa **8 funções** que serão testadas automaticamente pelo professor.

---

## Etapa 2 — Montar a estrutura de arquivos

Você precisará de **dois arquivos**:

```
📁 projeto/
├── caminho.h       ← fornecido pelo professor (não mexa)
└── SEUMATRICULA.c  ← único arquivo que você entrega
```

O `caminho.h` já está definido no PDF. Crie-o exatamente como está. Seu `.c` começa assim:

```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>   // para sqrt() em CalcularDistanciaGeometrica
#include "caminho.h"
```

---

## Etapa 3 — Implementar as funções, nesta ordem

A ordem importa porque algumas funções dependem de outras.

### 3.1 — `InicializarCaminho` (a mais importante)

É o ponto de entrada de tudo. Ela deve:

1. Validar a string (se tiver caractere inválido, retorna `NULL`)
2. Alocar o `Caminho`
3. Criar a lista encadeada de `Comando` a partir da string
4. Criar o nó de `Posicao` inicial com o ponto `(xInicial, yInicial)`
5. Simular o trajeto: percorrer os comandos e montar a lista de posições (`Historico`)
6. Preencher `Inicio`, `Fim`, `Historico`, `Instrucoes` e `N`

```c
Caminho * InicializarCaminho(const char *Sequencia, int xInicial, int yInicial) {
    // validar → alocar Caminho → montar lista de Comandos
    // → montar lista de Posicoes → preencher campos
}
```

---

### 3.2 — `DestruirCaminho`

Libera **toda** a memória alocada: lista de `Posicao` (Historico), lista de `Comando` e o próprio `Caminho`.

```c
void DestruirCaminho(Caminho * C) {
    // percorrer e liberar cada nó de Historico
    // percorrer e liberar cada nó de Instrucoes
    // liberar C
}
```

---

### 3.3 — `DeterminarFim`

Simples: retorna `C->Fim`, já calculado dentro de `InicializarCaminho`.

```c
Posicao * DeterminarFim(Caminho * C);
```

---

### 3.4 — `HistoricoPosicoes`

Retorna `C->Historico`, já montado dentro de `InicializarCaminho`.

```c
Posicao * HistoricoPosicoes(Caminho * C);
```

---

### 3.5 — `CalcularDistanciaTotal`

Retorna `C->N` — cada comando representa um passo unitário.

```c
int CalcularDistanciaTotal(Caminho * C);
```

---

### 3.6 — `CalcularDistanciaGeometrica`

Distância euclidiana em linha reta entre início e fim:

```
dE = sqrt((x2 - x1)² + (y2 - y1)²)
```

Use `C->Inicio` e `C->Fim`. Lembre-se de compilar com `-lm` para linkar a biblioteca matemática.

```c
double CalcularDistanciaGeometrica(Caminho * C);
```

---

### 3.7 — `CalcularDistanciaManhattan`

Soma dos deslocamentos horizontal e vertical:

```
dM = |x2 - x1| + |y2 - y1|
```

```c
int CalcularDistanciaManhattan(Caminho * C);
```

---

### 3.8 — `ContarInstrucoes`

Retorna `C->N` — o total de comandos executados.

```c
int ContarInstrucoes(Caminho * C);
```

---

## Etapa 4 — Testar com o `main` do professor

O PDF já traz um `main.c` pronto. O caso de teste principal é:

| Parâmetro | Valor |
|---|---|
| Sequência | `"LLLLLLLNNNNN"` |
| Posição inicial | `(2, 2)` |
| Posição final esperada | `(9, 7)` |
| Distância total | `12` |
| Distância geométrica | `8.60` |
| Distância de Manhattan | `12` |
| Quantidade de instruções | `12` |

**Compilar e executar:**

```bash
gcc SEUMATRICULA.c main.c -o robo.exe -lm
./robo.exe
```

Verifique se a saída bate exatamente com os valores esperados antes de entregar.

---

## Etapa 5 — Checklist antes de entregar

- [ ] Nenhum vetor usado para histórico ou comandos (somente listas encadeadas)
- [ ] Comandos inválidos retornam `NULL`, `-1` ou `-1.0` conforme o tipo da função
- [ ] `DestruirCaminho` libera **tudo** sem double-free ou memory leak
- [ ] O arquivo `caminho.h` **não foi modificado**
- [ ] Nome do arquivo de entrega é `NUMEROMATRICULA.c`
- [ ] Compila sem erros ou warnings com `gcc`
- [ ] Funções com ponteiro retornam `NULL` em caso de erro
- [ ] Funções `int` retornam `-1` em caso de erro
- [ ] Funções `double` retornam `-1.0` em caso de erro

---

## Resumo visual da dependência entre funções

```
InicializarCaminho
    ├── monta Instrucoes (lista de Comando)
    ├── monta Historico  (lista de Posicao)
    ├── define Inicio e Fim
    └── calcula N
         │
         ├── DeterminarFim        → retorna C->Fim
         ├── HistoricoPosicoes    → retorna C->Historico
         ├── CalcularDistanciaTotal    → usa C->N
         ├── ContarInstrucoes          → usa C->N
         ├── CalcularDistanciaGeometrica → usa C->Inicio e C->Fim
         ├── CalcularDistanciaManhattan  → usa C->Inicio e C->Fim
         └── DestruirCaminho      → libera tudo
```

---

> **Dica final:** comece implementando `InicializarCaminho` — ela é o coração do projeto. Se ela estiver correta, todas as outras funções ficam triviais.
