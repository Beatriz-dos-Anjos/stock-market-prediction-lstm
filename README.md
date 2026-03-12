# 📈 Previsão de Fechamento do Bitcoin (2017–2023) com LSTM

**Centro de Informática (CIn) - Universidade Federal de Pernambuco (UFPE)** 

**Disciplina:** Redes Neurais (IF702)

---

## Equipe
* Antonio Carolino
* Beatriz Mergulhão
* Luiza Trigueiro
* João Ebbers

---

## 📌 Sobre o Projeto

Este projeto tem como objetivo aplicar arquiteturas de **Redes Neurais Recorrentes (RNNs)**, especificamente **Long Short-Term Memory (LSTM)**, para prever o preço de fechamento do Bitcoin utilizando dados históricos do período de 2017 a 2023. 

A pesquisa foi dividida em múltiplos experimentos (trials) para investigar desde baselines simples até abordagens baseadas em retornos logarítmicos, técnicas de regularização (Dropout e L2) e arquiteturas mais profundas (Stacked LSTMs e xLSTM).

---

## 🛠️ Metodologia e Pipeline de Dados

O pipeline de processamento e avaliação foi construído para evitar o vazamento de dados (*data leakage*) e utilizar as melhores práticas para a modelagem de séries temporais:

* **Split Temporal:** Divisão sequencial de 80% para treino e 20% para teste (sem embaralhamento dos dados temporais).
* **Normalização:** Utilização de `MinMaxScaler` (para preços absolutos) e `RobustScaler` (para log-returns), com o ajuste (*fit*) aplicado exclusivamente no conjunto de treino.
* **Criação de Janelas (Sliding Windows):** Geração de sequências temporais (ex: janelas de 30 dias) para prever o próximo passo no tempo (horizonte de 1 dia).
* **Baseline Naïve:** O modelo assume que o valor do próximo dia será igual ao do dia atual (último valor da janela), servindo como base mínima de comparação.

---

## 📏 Métricas de Avaliação

O desempenho dos modelos foi medido utilizando as seguintes métricas:

* **MSE** (Mean Squared Error)
* **RMSE** (Root Mean Squared Error)
* **MAE** (Mean Absolute Error)
* **POCID** (Prediction of Change in Direction) - Avalia a porcentagem de acertos na *direção* da tendência do ativo (subida ou descida).

---

## 🧪 Experimentos (Trials)

O projeto evoluiu através de três principais estágios de experimentação:

### 1. Baseline LSTM (Predição de Preço)
Implementação de um modelo LSTM padrão focado no preço de fechamento.  
**Hiperparâmetros base:** Janela de 30 dias, Batch size de 32, 50 épocas, 64 unidades LSTM, Learning rate de 1e-4, *Early Stopping* e *ReduceLROnPlateau*.

### 2. Forecasting de Log-Returns
Ajuste no pipeline para prever **retornos logarítmicos** em vez do preço absoluto, visando maior estabilidade matemática.  
**Características:** Janela menor (7 dias) e uso do `RobustScaler` para mitigar a alta volatilidade e os *outliers* típicos do mercado de criptomoedas.

### 3. Regularização e Arquiteturas Avançadas
Foco em reduzir o *overfitting* e melhorar a capacidade de generalização do modelo:
* **Tuning de Dropout:** Testes com taxas variando de 0.0 a 0.4.
* **Regularização L2:** Aplicação de penalidades nos pesos da rede.
* **Arquiteturas Empilhadas:** Testes com múltiplas camadas LSTM no Keras (Two-Layer Stacked LSTM) e comparações de desempenho com uma implementação PyTorch do xLSTM.

---

## 📊 Principais Resultados Consolidados

Abaixo está o resumo comparativo das abordagens na predição de preços (com base no conjunto de teste):

| Arquitetura | RMSE | MAE | POCID | Melhoria RMSE vs Baseline Inicial (%) |
| :--- | :--- | :--- | :--- | :--- |
| **Initial LSTM Baseline (Dropout 0.0)** | 1501.01 | 1158.97 | **54.68%** | 0.00% |
| **Best Dropout LSTM (Rate: 0.3)** | 1440.09 | 1035.22 | **56.40%** | +4.06% |
| **Two-Layer Stacked LSTM (Keras)** | 1493.03 | 1219.02 | 48.03% | +0.53% |
| **PyTorch XLSTM** | **690.59** | **533.67** | 46.31% | **+53.99%** |

### 💡 Conclusões
* O modelo **PyTorch XLSTM** demonstrou o melhor desempenho absoluto na redução do erro de magnitude do preço (RMSE e MAE).
* O modelo Keras com **Dropout de 0.3** apresentou o melhor equilíbrio geral. Apesar de um RMSE maior que o xLSTM, ele obteve a maior taxa de acerto na direção da tendência (**POCID de 56.40%**), o que é uma métrica extremamente valiosa em aplicações financeiras práticas.
