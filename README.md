# 📊 WDOFUT Quant Model: Suportes e Resistências Estatísticos

![Status](https://img.shields.io/badge/Status-Concluído-success)
![Stack](https://img.shields.io/badge/Stack-Databricks%20|%20Pure%20Spark%20|%20PowerBI-blue)
![Model](https://img.shields.io/badge/Model-Gaussian%20Distribution-red)

> **"O mercado não é linear, é probabilístico. Por que usar linhas estáticas?"**

Este projeto implementa um modelo de **Análise Quantitativa** para o Mini Dólar Futuro (WDOFUT). Diferente dos Pivot Points tradicionais (que usam médias simples), este pipeline calcula níveis de preço baseados na **Volatilidade Histórica (Desvio Padrão)** do ativo.

O objetivo é entregar ao trader/analista zonas de probabilidade estatística de reversão (Suporte/Resistência) em um Dashboard de alta performance conectado ao Databricks.

---

## 🖼️ Visão do Analista (Dashboard)

O painel exibe os níveis calculados (R1, R2, R3, S1, S2, S3) sobrepostos ao preço, permitindo identificar exaustão de movimento.

<img width="1919" height="1079" alt="Dashboard WDOFUT Gauss" src="https://github.com/user-attachments/assets/5704b4c1-2a04-4008-bd4a-76f32250e6c2" />

---

## 🧠 A Lógica Quant (Camada Silver)

O diferencial analítico deste projeto está na substituição da aritmética simples pela estatística:

1.  **Problema:** Pivot Points clássicos ignoram se o mercado está volátil ou calmo.
2.  **Solução Quant:** Calcular a amplitude média (`Máxima - Mínima`) e seu **Desvio Padrão ($\sigma$)**.
3.  **Aplicação:** Os níveis são projetados a partir do Fechamento usando múltiplos de $\sigma$ (0.5, 1.0, 1.5), criando um "Envelope de Volatilidade".
    * *R1:* Fechamento + 0.5$\sigma$
    * *R3 (Exaustão):* Fechamento + 1.5$\sigma$

[Ver implementação estatística (Silver)](silver.ipynb)

---

## 🛠️ Engenharia de Alta Performance

Para garantir que o cálculo estatístico seja entregue em tempo hábil para tomada de decisão, o pipeline foi otimizado:

### 1. Ingestão de Banco Transacional (Bronze)
* Simulação de um cenário real corporativo: Conexão via driver JDBC/Psycopg2 em um banco **PostgreSQL** (Origem) para capturar os dados brutos de negociação.
* [Ver código Bronze](bronze.ipynb)

### 2. Pure Spark (Sem Pandas)
* **Desafio Técnico:** O projeto foi construído 100% com a API nativa do **PySpark**, sem o uso de Pandas. Isso garante que o modelo seja escalável para Terabytes de dados históricos sem estourar a memória (OOM).

### 3. Otimização de Leitura (Gold)
* Uso do comando **`OPTIMIZE`** e **`ZORDER BY data_pregao`**.
* **Impacto:** O Power BI (via DirectQuery) consegue filtrar datas específicas instantaneamente, pois o Databricks ignora arquivos desnecessários (Data Skipping).
* [Ver otimização Gold](gold.ipynb)

---

## 💻 Tech Stack

* **Modelagem:** Estatística Descritiva (Curva de Gauss/Desvio Padrão).
* **Processamento:** Azure Databricks (Spark SQL & PySpark).
* **Database:** PostgreSQL (Source) -> Delta Lake (Target).
* **Visualização:** Power BI (DirectQuery).
