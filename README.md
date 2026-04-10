# NLP Parallel Processing with MPI 🚀

Este repositório contém um experimento de processamento de linguagem natural (NLP) focado em **similaridade de texto** e **computação de alto desempenho (HPC)**, comparando execuções seriais e paralelas.

## 📌 1. Descrição do Problema
O objetivo é avaliar a escalabilidade do cálculo de similaridade entre um grande volume de dados utilizando o algoritmo de **Similaridade de Jaccard**.

* **Dataset:** Quora Question Pairs (`nlp_features_train.csv`).
* **Volume:** 5.000 perguntas únicas $\rightarrow$ **12.497.500 pares de comparação**.
* **Pipeline:** Tokenização, limpeza de texto e cálculo de interseção/união de palavras.

## 🛠️ 2. Metodologia de Paralelização
A transição do script serial (`avaliador.py`) para o distribuído (`avaliadormpi.py`) utilizou a biblioteca **mpi4py**.

### Estratégia de Divisão:
* **Divisão de Carga:** O espaço de iteração do laço externo ($i$) é particionado entre os processos.
* **Comunicação (Master/Worker):** * O **Rank 0** realiza o *broadcast* do dataset.
    * Os trabalhadores processam suas fatias.
    * O comando *gather* consolida os resultados finais no mestre.

## 📊 3. Resultados e Performance
## 📊 3. Resultados e Performance

Os testes foram realizados variando de 1 a 12 processos (threads).

| Processos | Tempo (s) | Speedup | Eficiência |
| :---: | :---: | :---: | :---: |
| 1 | 40,30 | 1,00 | 1,00 |
| 2 | 30,20 | 1,33 | 0,67 |
| 4 | 20,15 | 2,00 | 0,50 |
| 8 | 14,80 | 2,72 | 0,34 |
| 12 | 12,80 | 3,15 | 0,26 |

### Principais Constatações:
* **Redução de Tempo:** O tempo total de processamento caiu em **65%**.
* **Speedup Máximo:** 3,15x com 12 processos.

## ⚠️ 4. Análise Crítica
1.  **Gargalo de Comunicação:** O custo de transferência de dados via MPI (overhead) torna-se significativo a partir de 8 processos.
2.  **Desbalanceamento de Carga:** Como o laço interno diminui de tamanho conforme o índice $i$ avança, os primeiros processos realizam mais trabalho que os últimos.

> **Sugestão de Melhoria:** Implementar uma **distribuição cíclica** (Round-Robin) para garantir que todos os cores trabalhem de forma equitativa até o final da execução.

## 🚀 Como Executar

### Pré-requisitos
* Python 3.x
* MPI (OpenMPI ou MPICH)
* `pip install mpi4py pandas`

### Comando de Execução
Para rodar com, por exemplo, 4 processos:
```bash
mpirun -np 4 python avaliadormpi.py
