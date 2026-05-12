# 🤖 Trajetória de um Robô — TP02

> Trabalho Prático 02 — Estrutura de Dados | Universidade Católica de Brasília  
> 1º Semestre de 2026

Simulação de trajetórias de um robô autônomo em um plano cartesiano, implementada em **linguagem C** com uso de **listas encadeadas** (sem vetores). O sistema armazena, processa, otimiza e analisa caminhos a partir de sequências de comandos direcionais.

---

## 📋 Índice

- [Sobre o projeto](#-sobre-o-projeto)
- [Comandos do robô](#-comandos-do-robô)
- [Estruturas de dados](#-estruturas-de-dados)
- [Funções implementadas](#-funções-implementadas)
- [Como compilar e executar](#-como-compilar-e-executar)
- [Exemplo de uso](#-exemplo-de-uso)
- [Saída esperada](#-saída-esperada)
- [Estrutura de arquivos](#-estrutura-de-arquivos)
- [Restrições do projeto](#-restrições-do-projeto)
- [Tratamento de erros](#-tratamento-de-erros)

---

## 📖 Sobre o projeto

Um robô autônomo se desloca em um plano cartesiano a partir de uma posição inicial `(x, y)`. Seus movimentos são representados por comandos unitários nas direções **Norte**, **Sul**, **Leste** e **Oeste**, armazenados em uma lista encadeada.

O objetivo é implementar um sistema completo capaz de:

- Inicializar e destruir caminhos dinamicamente
- Registrar o histórico de posições visitadas
- Calcular distâncias total, euclidiana e de Manhattan
- Contar o número de instruções executadas

---

## 🧭 Comandos do robô

| Comando | Direção | Efeito na posição |
|:---:|:---:|:---|
| `'N'` | Norte | `(x, y)` → `(x, y + 1)` |
| `'S'` | Sul | `(x, y)` → `(x, y - 1)` |
| `'L'` | Leste | `(x, y)` → `(x + 1, y)` |
| `'O'` | Oeste | `(x, y)` → `(x - 1, y)` |

Qualquer caractere diferente desses quatro é considerado **inválido** e causa retorno de erro.

---

## 🗂 Estruturas de dados

Todas definidas no arquivo `caminho.h` (não modificável):

```c
typedef struct Comando {
    char Direcao;           // 'N', 'S', 'L' ou 'O'
    struct Comando *Proximo;
} Comando;

typedef struct Posicao {
    int X;                  // Abscissa do ponto
    int Y;                  // Ordenada do ponto
    struct Posicao *Proximo;
} Posicao;

typedef struct Caminho {
    Posicao *Inicio;        // Primeira posição ocupada pelo robô
    Posicao *Fim;           // Última posição ocupada pelo robô
    Posicao *Historico;     // Histórico de posições visitadas
    Comando *Instrucoes;    // Lista de instruções a executar
    int N;                  // Quantidade de instruções executadas
} Caminho;
```

---

## ⚙️ Funções implementadas

### `InicializarCaminho`
```c
Caminho * InicializarCaminho(const char *Sequencia, int xInicial, int yInicial);
```
Inicializa o caminho a partir de uma string de comandos e das coordenadas iniciais. Valida os comandos, monta as listas encadeadas de instruções e posições, e preenche todos os campos da struct.

---

### `DestruirCaminho`
```c
void DestruirCaminho(Caminho *C);
```
Libera toda a memória alocada: lista de posições, lista de comandos e a própria struct `Caminho`.

---

### `DeterminarFim`
```c
Posicao * DeterminarFim(Caminho *C);
```
Retorna um ponteiro para a última posição visitada pelo robô.

---

### `HistoricoPosicoes`
```c
Posicao * HistoricoPosicoes(Caminho *C);
```
Retorna o início do encadeamento com todas as posições visitadas, na ordem em que foram percorridas.

---

### `CalcularDistanciaTotal`
```c
int CalcularDistanciaTotal(Caminho *C);
```
Retorna a soma dos deslocamentos unitários realizados — equivalente ao número total de comandos executados.

---

### `CalcularDistanciaGeometrica`
```c
double CalcularDistanciaGeometrica(Caminho *C);
```
Calcula a distância em linha reta (euclidiana) entre a posição inicial e a final:

$$d_E = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}$$

---

### `CalcularDistanciaManhattan`
```c
int CalcularDistanciaManhattan(Caminho *C);
```
Calcula a distância de Manhattan entre a posição inicial e a final — representa o número mínimo de comandos necessários para atingir o destino:

$$d_M = |x_2 - x_1| + |y_2 - y_1|$$

---

### `ContarInstrucoes`
```c
int ContarInstrucoes(Caminho *C);
```
Retorna o número total de instruções executadas no trajeto.

---

## 🔨 Como compilar e executar

> **Requisito:** compilador GCC instalado (ambiente Windows, conforme laboratórios da UCB).

```bash
gcc SEUMATRICULA.c main.c -o robo.exe -lm
./robo.exe
```

Substitua `SEUMATRICULA` pelo número real da sua matrícula (ex: `26209999.c`).

A flag `-lm` é necessária para linkar a biblioteca matemática usada em `CalcularDistanciaGeometrica`.

---

## 💡 Exemplo de uso

```c
#include <stdio.h>
#include <stdlib.h>
#include "caminho.h"

void ExibirHistorico(Posicao *P) {
    while (P != NULL) {
        printf("(%d, %d)", P->X, P->Y);
        if (P->Proximo != NULL) printf(" -> ");
        P = P->Proximo;
    }
    printf("\n");
}

int main() {
    /*
     * Sequência: LLLLLLLNNNNN
     * Posição inicial: (2, 2)
     * 7 comandos Leste  → x vai de 2 a 9
     * 5 comandos Norte  → y vai de 2 a 7
     * Posição final esperada: (9, 7)
     */
    Caminho *C = InicializarCaminho("LLLLLLLNNNNN", 2, 2);
    if (C == NULL) {
        printf("Erro ao inicializar o caminho.\n");
        return 1;
    }

    Posicao *Fim = DeterminarFim(C);
    if (Fim != NULL)
        printf("Posicao final: (%d, %d)\n", Fim->X, Fim->Y);

    Posicao *Historico = HistoricoPosicoes(C);
    printf("Historico de posicoes:\n");
    ExibirHistorico(Historico);

    printf("Distancia total percorrida: %d\n",  CalcularDistanciaTotal(C));
    printf("Distancia geometrica: %.2f\n",       CalcularDistanciaGeometrica(C));
    printf("Distancia de Manhattan: %d\n",       CalcularDistanciaManhattan(C));
    printf("Quantidade de instrucoes: %d\n",     ContarInstrucoes(C));

    DestruirCaminho(C);
    return 0;
}
```

---

## ✅ Saída esperada

```
Posicao final: (9, 7)
Historico de posicoes:
(2, 2) -> (3, 2) -> (4, 2) -> (5, 2) -> (6, 2) -> (7, 2) -> (8, 2) -> (9, 2) -> (9, 3) -> (9, 4) -> (9, 5) -> (9, 6) -> (9, 7)
Distancia total percorrida: 12
Distancia geometrica: 8.60
Distancia de Manhattan: 12
Quantidade de instrucoes: 12
```

---

## 📁 Estrutura de arquivos

```
📁 projeto/
├── caminho.h          ← cabeçalho fornecido pelo professor (não modificar)
├── main.c             ← arquivo de testes fornecido pelo professor
├── SEUMATRICULA.c     ← implementação (único arquivo entregue)
└── README.md          ← este arquivo
```

---

## 🚫 Restrições do projeto

| Restrição | Detalhe |
|---|---|
| **Sem vetores** | O histórico de posições e a lista de comandos devem usar exclusivamente listas encadeadas |
| **`caminho.h` imutável** | O arquivo de cabeçalho não pode ser alterado em hipótese alguma |
| **Padrão C** | Código compilado com GCC no Windows (laboratórios UCB) |
| **Arquivo único** | Entrega de um único `.c` nomeado com o número de matrícula |
| **Trabalho individual** | Trabalhos semelhantes podem receber nota zero |

---

## ⚠️ Tratamento de erros

| Tipo de retorno | Valor em caso de erro |
|:---:|:---:|
| Ponteiro (`*`) | `NULL` |
| `int` | `-1` |
| `double` | `-1.0` |

Situações de erro incluem: ponteiro `NULL` recebido como parâmetro, comando inválido na sequência, falha na alocação de memória.

---

## 📐 Conceitos aplicados

- **TAD Lista** com encadeamento via ponteiros
- **Alocação dinâmica** de memória (`malloc` / `free`)
- **Distância euclidiana** (teorema de Pitágoras)
- **Distância de Manhattan** (métrica em grade)
- **Manipulação de ponteiros** e encadeamentos em C

---

*Atividade Avaliativa — vale 1,0 ponto | Penalização de 10% ao dia por atraso*
