# Seleção de atributos biomédicos com BHOA e SVM linear

Projeto acadêmico de aprendizado de máquina para investigar **seleção de atributos em dados de expressão gênica associados a tumores**. O notebook implementa uma busca de subconjuntos de genes com uma população de soluções binárias baseada no BHOA e mede cada solução usando um classificador `SVC` linear.

O objetivo não é apenas obter uma boa classificação: é encontrar um conjunto menor de atributos que preserve o desempenho e torne o experimento mais interpretável e menos custoso.

## Problema e ideia central

Datasets de expressão gênica costumam ter muitas colunas para poucas amostras. Treinar um classificador com todos os genes pode aumentar o ruído, o tempo de execução e o risco de overfitting. Aqui, cada solução candidata é uma máscara binária:

```text
1 0 0 1 0 1 ...  →  usar os genes 1, 4 e 6
```

O algoritmo procura máscaras com dois objetivos combinados:

```text
fitness = 0.99 × acurácia da SVM
        + 0.01 × (1 − proporção de atributos selecionados)
```

Assim, a acurácia domina a decisão, mas subconjuntos menores recebem uma pequena preferência.

## Pipeline completo

1. Carregar um dos três datasets usados no notebook.
2. Separar a coluna de classe das variáveis de expressão gênica.
3. Imputar valores ausentes pela média da coluna, quando necessário.
4. Aplicar um filtro MRMR simplificado, opcional, para reduzir o espaço de busca a até 100 atributos.
5. Inicializar a população de soluções e evoluí-la com o BHOA.
6. Usar funções de transferência S-shaped/X-shaped e crossover para converter/alterar posições binárias.
7. Avaliar cada máscara com `SVC(kernel="linear")` e validação cruzada estratificada.
8. Comparar fitness, acurácia estimada, quantidade de genes selecionados e tempo de execução.

### O que o MRMR faz neste notebook

O filtro implementado combina:

- **relevância:** informação mútua entre cada atributo e a classe;
- **redundância:** média da correlação absoluta de Pearson com os atributos já escolhidos;
- **critério guloso:** selecionar atributos relevantes e pouco redundantes.

O resultado do MRMR é apenas uma pré-seleção. Os índices finais são recuperados em relação ao conjunto original de genes.

## Configuração experimental

| Parâmetro | Valor padrão | Papel |
|---|:---:|---|
| População (`n_cavalos`) | 35 | Número de soluções candidatas por iteração. |
| Iterações | 60 | Número de atualizações da população. |
| `alfa` | 0.99 | Peso da acurácia no fitness. |
| `beta` | 0.01 | Peso da redução de atributos. |
| Pré-filtro MRMR | Ativo | Limita o espaço de busca a 100 atributos. |
| Validação | 10 folds | `RepeatedStratifiedKFold`, uma repetição, `random_state=42`. |
| Classificador | SVM linear | Avalia cada subconjunto de atributos. |

## Datasets utilizados

O notebook principal (`e.ipynb`) executa os experimentos com:

| Arquivo | Coluna de classe | Organização esperada |
|---|---|---|
| `Lymphoma.xlsx` | `class` | As demais colunas são atributos. |
| `Brain_Tumor1.xlsx` | primeira coluna | As colunas restantes são genes. |
| `Prostate_Tumor.xlsx` | primeira coluna | As colunas restantes são genes. |

A pasta `os outros datasets/` contém outras planilhas/arquivos de referência, mas eles não são carregados pela execução principal do notebook.

Os dados `.xlsx` e `.arff` são ignorados pelo Git por causa do tamanho e da origem externa. Para reproduzir o experimento, obtenha os dados de uma fonte autorizada e coloque os três arquivos exatamente ao lado de `e.ipynb`:

```text
Trabalho BHOA V5/
├── Trabalho BHOA V5/
│   ├── e.ipynb
│   ├── Lymphoma.xlsx
│   ├── Brain_Tumor1.xlsx
│   └── Prostate_Tumor.xlsx
└── os outros datasets/
```

## Como executar

Na raiz do repositório:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install numpy pandas scikit-learn scipy openpyxl jupyter mrmr-selection tabulate
jupyter notebook "Trabalho BHOA V5/Trabalho BHOA V5/e.ipynb"
```

Também é possível abrir o notebook no VS Code ou no JupyterLab. Execute as células na ordem; o treinamento pode demorar porque cada candidato é avaliado por validação cruzada.

## O que o notebook produz

Para cada dataset, são exibidos:

- formato e distribuição das classes;
- melhor máscara de atributos;
- índices dos atributos selecionados no espaço original;
- fitness final;
- acurácia estimada a partir do fitness;
- quantidade de genes escolhidos;
- tempo total de execução;
- uma tabela-resumo comparativa.

## Como interpretar os resultados

Uma solução é interessante quando mantém a acurácia da SVM com uma fração menor dos genes. A comparação deve considerar conjuntamente:

- **acurácia:** qualidade da classificação;
- **fitness:** compromisso entre acurácia e compactação;
- **número de genes:** custo e potencial interpretabilidade;
- **tempo:** custo computacional da busca.

### Limitações importantes

- Os resultados são experimentais e dependem da semente, dos datasets e dos hiperparâmetros.
- O notebook executa uma única rodada principal; portanto, a tabela não mede a variabilidade entre várias execuções independentes.
- As colunas `Best`, `Mean`, `Worst` e `STD` da tabela são preenchidas com a mesma estimativa na função atual; elas não representam estatísticas de múltiplas rodadas.
- A validação é feita com SVM linear e não deve ser interpretada como prova de que outros classificadores teriam o mesmo desempenho.
- O projeto usa dados biomédicos para fins educacionais e não seleciona biomarcadores clínicos nem produz diagnóstico.
