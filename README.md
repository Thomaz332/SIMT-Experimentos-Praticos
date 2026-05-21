# SIMT — Experimentos Práticos em CUDA

**Universidade Católica de Santos — UNISANTOS**  
Arquitetura de Computadores · Turma CO.N1.17 · Prof. Walter Silva Oliveira

**Autores:** João Pedro Thomaz Kairalla dos Santos & Thiago Danilow de Araújo

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Thomaz332/SIMT-Experimentos-Praticos/blob/main/PCG_Walter_v2.ipynb)

---

## Sobre o projeto

Este repositório contém a implementação prática do trabalho acadêmico sobre o modelo de execução **SIMT** (*Single Instruction, Multiple Threads*), paradigma introduzido pela NVIDIA na arquitetura Tesla (2006). O SIMT organiza *threads* em grupos de 32 denominados *warps*, que executam a mesma instrução simultaneamente — porém cada *thread* mantém registradores próprios e pode divergir em desvios condicionais.

Os experimentos foram executados em **Google Colab** com GPU **NVIDIA Tesla T4** (CUDA 13.0).

---

## Experimentos

### Experimento 1 — Paralelismo Básico: Soma de Vetores

Demonstra o ganho bruto de desempenho do modelo SIMT sobre execução sequencial na CPU. Cada *thread* processa exatamente um elemento do vetor, com acessos totalmente coalescidos e sem divergência — condições ideais para o SIMT.

| Configuração | Tempo | Ganho |
|---|---|---|
| CPU (sequencial) | 99,37 ms | — |
| GPU — SIMT (N = 2²⁴) | 0,79 ms | **×125,1** |

---

### Experimento 2 — Divergência de Warps

Isola e quantifica o impacto da divergência de *warps* — fenômeno em que *threads* do mesmo *warp* tomam caminhos distintos num desvio condicional, forçando a serialização dos ramos pelo hardware SIMT. O `if/else` baseado em `idx % 2` configura o pior caso teórico: divisão exatamente 50/50 do *warp*.

| Configuração | Tempo | Overhead |
|---|---|---|
| Sem divergência | 0,30 ms | — |
| Com divergência (N = 2²², 100 iter.) | 3,12 ms | **+944,7 %** |

---

### Experimento 3 — Memória Compartilhada em Multiplicação de Matrizes

Compara o uso de memória global versus *shared memory* com *tiling* (blocos 16×16) na multiplicação de matrizes 1024×1024. O *tiling* reduz acessos à memória global de N para N/16 = 64 por elemento-resultado.

| Configuração | Tempo | Ganho |
|---|---|---|
| Memória global (ingênua) | 5,29 ms | — |
| *Shared memory* — tiling 16×16 | 3,31 ms | **×1,6** |

---

## Resultados consolidados

```
GPU: NVIDIA Tesla T4  •  CUDA 13.0  •  Driver 580.82.07
```

---

## Como executar

1. Abra o notebook no Google Colab clicando no badge acima
2. Conecte uma sessão com GPU (Runtime → Change runtime type → T4 GPU)
3. Execute as células em ordem — cada experimento compila e roda automaticamente
4. A célula de consolidação gera o gráfico comparativo ao final

**Requisitos:** Google Colab com GPU NVIDIA + CUDA (sem instalação local necessária)

---

## Estrutura do repositório

```
.
├── PCG_Walter_v2.ipynb     # Notebook com os 3 experimentos CUDA
└── README.md
```

---

## Referências

1. LINDHOLM, E. et al. *NVIDIA Tesla: A Unified Graphics and Computing Architecture*. **IEEE Micro**, v. 28, n. 2, p. 39–55, 2008.
2. KIRK, D. B.; HWU, W. W. *Programming Massively Parallel Processors: A Hands-on Approach*. 3. ed. Morgan Kaufmann, 2016.
3. PATTERSON, D.; HENNESSY, J. *Computer Organization and Design: ARM Edition*. Morgan Kaufmann, 2016.
4. NVIDIA Corporation. *CUDA C++ Programming Guide*, v. 12.0, 2022.
