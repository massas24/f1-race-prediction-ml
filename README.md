# 🏎️ Previsão de Resultados na Fórmula 1 (1950–2024)

> Projeto de Data Science e Machine Learning para prever a probabilidade de um piloto terminar no Top 3 numa corrida de Fórmula 1.

---

## 📌 Objetivo

Desenvolver um pipeline completo de Data Science utilizando o dataset **"Formula 1 World Championship (1950–2024)"** do Kaggle, com o objetivo de prever o desempenho de pilotos em corridas de Fórmula 1 — especificamente, a probabilidade de terminarem no **Top 3 (pódio)**.

Este é um problema de **classificação binária**:
- `1` → piloto terminou no Top 3
- `0` → piloto não terminou no Top 3

---

## 📊 Dataset

O dataset contém dados históricos completos da Fórmula 1 desde 1950 até 2024, distribuídos por vários ficheiros CSV normalizados:

| Ficheiro | Conteúdo |
|---|---|
| `results.csv` | Posição final, grelha, pontos, voltas por corrida/piloto |
| `races.csv` | Datas, circuitos, época e ronda de cada corrida |
| `drivers.csv` | Informação sobre os pilotos |
| `constructors.csv` | Informação sobre as equipas |

---

## 🛠️ Tecnologias

- **Python 3**
- **Pandas** — manipulação e limpeza de dados
- **Scikit-learn** — modelação e avaliação
- **Matplotlib** — visualização de resultados

---

## 🗂️ Estrutura do Projeto

```
f1-prediction/
│
├── data/                   # Ficheiros CSV do dataset (Kaggle)
│   ├── results.csv
│   ├── races.csv
│   ├── drivers.csv
│   └── constructors.csv
│
├── f1_prediction.ipynb     # Notebook principal com todo o pipeline
└── README.md
```

---

## 🧠 Abordagem

### 1. Data Preprocessing

- Carregamento dos ficheiros com tratamento de valores nulos (`\N` → `NaN`)
- Merge entre `results.csv` e `races.csv` via `raceId` (inner join)
- Seleção de colunas relevantes para o modelo
- Criação da variável target `top3` com base em `positionOrder <= 3`
- Remoção de registos sem posição final (DNFs e não-classificados)
- Eliminação da coluna `fastestLap` (dados incompletos e irrelevantes para o target)
- Substituição de `grid = 0` (pit lane starts) pela mediana da coluna
- Ordenação cronológica do dataset por data de corrida

### 2. Feature Engineering

Foram criadas as seguintes features preditivas:

| Feature | Descrição |
|---|---|
| `grid` | Posição de partida na grelha |
| `avg_position_last5` | Média das posições nas últimas 5 corridas do piloto |
| `nTop3Last5Race` | Número de Top 3 nas últimas 5 corridas do piloto |
| `team_avg_points_last5` | Média de pontos da equipa nas últimas 5 corridas |
| `driver_experience` | Número total de corridas disputadas pelo piloto até à data |

> ⚠️ Todas as features de histórico utilizam `.shift(1)` para evitar **data leakage** — garantindo que apenas informação anterior à corrida atual é usada.

### 3. Modelação

Foram testados três modelos de classificação:

| Modelo | Accuracy | Precision (Top 3) | Recall (Top 3) | F1 (Top 3) |
|---|---|---|---|---|
| Logistic Regression | 0.85 | 0.71 | 0.67 | 0.69 |
| Logistic Regression (balanced) | 0.82 | 0.59 | 0.87 | 0.71 |
| Random Forest | 0.83 | 0.67 | 0.62 | 0.65 |
| Random Forest (balanced) | 0.84 | 0.70 | 0.63 | 0.66 |
| Gradient Boosting | 0.85 | 0.71 | 0.66 | 0.69 |

### 4. Avaliação

Métricas utilizadas:
- **Accuracy** — percentagem de previsões corretas no total
- **Precision** — de todos os previstos como Top 3, quantos realmente o eram
- **Recall** — de todos os Top 3 reais, quantos foram corretamente previstos
- **F1-score** — média harmónica entre Precision e Recall

Split utilizado: **80% treino / 20% teste** com `random_state=42`.

### 5. Feature Importance

Com base no modelo Random Forest:

```
grid                    ██████████████████  ~0.30  ← mais importante
avg_position_last5      ████████████        ~0.21
team_avg_points_last5   ████████            ~0.17
driver_experience       ████████            ~0.17
nTop3Last5Race          ███████             ~0.14
```

---

## 📈 Resultados e Conclusões

- A **posição de grelha** (`grid`) é o fator mais preditivo para o Top 3, o que faz sentido no contexto da F1 — partir da frente é uma vantagem enorme.
- A **performance recente** do piloto (`avg_position_last5`) é o segundo fator mais importante.
- A **força da equipa** tem um peso relevante, demonstrando que o hardware é determinante neste desporto.
- O dataset apresenta **desbalanceamento de classes** (~88% não Top 3, ~12% Top 3), o que foi tratado com o parâmetro `class_weight='balanced'`.

---

## 🚀 Extensões Futuras

- [ ] Adicionar feature de consistência do piloto (% corridas terminadas sem DNF)
- [ ] Incluir dados de circuito (historial do piloto em cada pista)
- [ ] Prever vencedores de corridas (Top 1)
- [ ] Prever pontos por temporada
- [ ] Comparar pilotos dentro da mesma equipa
- [ ] Otimização de hiperparâmetros com GridSearchCV
- [ ] Criar dashboard interativo com Plotly/Dash

---

## ⚙️ Como Executar

1. Faz download do dataset em [Kaggle — Formula 1 World Championship](https://www.kaggle.com/datasets/rohanrao/formula-1-world-championship-1950-2020)
2. Coloca os ficheiros CSV na pasta `data/`
3. Instala as dependências:

```bash
pip install pandas scikit-learn matplotlib
```

4. Abre e executa o notebook `f1_prediction.ipynb` do início ao fim

---

## 👤 Autor

Projeto desenvolvido como exercício de aprendizagem em **Data Science e Machine Learning**, combinando análise exploratória de dados, feature engineering, modelação preditiva e visualização de resultados.
