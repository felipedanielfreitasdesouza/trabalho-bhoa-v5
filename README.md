# Seleção de atributos com BHOA e SVM

Projeto de aprendizado de máquina aplicado à classificação de dados de expressão gênica associados a tumores. O notebook implementa uma estratégia de seleção de atributos baseada no algoritmo BHOA e avalia os subconjuntos escolhidos com um classificador SVM linear.

## Objetivo

Reduzir a dimensionalidade de conjuntos de dados biomédicos, mantendo atributos relevantes para a classificação e evitando que o modelo dependa de milhares de variáveis pouco informativas.

## Pipeline

1. Carregamento dos conjuntos de Lymphoma, Brain Tumor e Prostate Tumor.
2. Codificação dos rótulos e tratamento de valores ausentes.
3. Pré-seleção opcional de atributos pelo critério MRMR.
4. Busca de subconjuntos com BHOA, funções de transferência e crossover.
5. Avaliação dos subconjuntos com SVM linear e validação cruzada.
6. Comparação dos resultados de acurácia, fitness e quantidade de atributos selecionados.

## Tecnologias

- Python
- Jupyter Notebook
- NumPy e Pandas
- scikit-learn
- SciPy
- openpyxl

## Como executar

Instale as dependências:

```bash
pip install numpy pandas scikit-learn scipy openpyxl jupyter
```

Os dados precisam estar na mesma pasta do notebook. Depois, execute:

```bash
cd "Trabalho BHOA V5/Trabalho BHOA V5"
jupyter notebook e.ipynb
```

Os arquivos de dados não são versionados neste repositório por causa do tamanho dos conjuntos e da origem externa dos dados. O notebook documenta os nomes esperados: `Lymphoma.xlsx`, `Brain_Tumor1.xlsx` e `Prostate_Tumor.xlsx`.

## Estrutura

```text
Trabalho BHOA V5/
├── Trabalho BHOA V5/e.ipynb
└── os outros datasets/
```

## Observação

Este é um projeto acadêmico de seleção de atributos e classificação. Os resultados devem ser interpretados como experimentais, não como ferramenta de diagnóstico médico.
