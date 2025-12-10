# 📊 WDOFUT: Análise de Níveis de Preço e Volatilidade

> **Projeto de Engenharia de Dados que implementa um modelo estatístico (Curva de Gauss) para definir Suportes e Resistências do Mini Dólar Futuro (WDOFUT).**

![Status](https://img.shields.io/badge/Status-Concluído-success)
![Stack](https://img.shields.io/badge/Stack-Databricks%20|%20Spark%20|%20PowerBI-blue)
![Modelo](https://img.shields.io/badge/Modelo-Curva%20de%20Gauss-red)

## 🖼️ Visão Geral do Dashboard
<img width="1919" height="1079" alt="Image" src="https://github.com/user-attachments/assets/5704b4c1-2a04-4008-bd4a-76f32250e6c2" />

---

## O Problema de Negócio

No mercado de derivativos (como o Mini Dólar), a definição precisa de **Níveis de Suporte e Resistência** é fundamental para o gerenciamento de risco.

O desafio central deste projeto foi:
1.  **Melhorar a Projeção:** Substituir o cálculo padrão de Pivot Points (que usa apenas a máxima, mínima e fechamento do dia anterior) por um modelo estatístico robusto.
2.  **Garantir a Performance:** Entregar os níveis de preço prontos para consumo via **consulta direta** ao banco de dados, seguindo o princípio da Arquitetura de Dados moderna (ELT).

---

## 🛠️ A Solução Técnica (Lakehouse ELT)

Construímos um pipeline de dados ponta a ponta (**End-to-End**) no ambiente Databricks, focado em alta performance e governança de dados.

### Arquitetura do Pipeline (ELT Medalhão)

A arquitetura garante que todos os cálculos complexos sejam feitos na camada de transformação, antes da visualização:

* Ingestão (Bronze):** Dados brutos de cotação de área (abertura, máxima, mínima, fechamento).
* Engenharia (Silver):** Foco no **Modelo Estatístico da Curva de Gauss** (detalhado abaixo).
* Modelagem (Gold):** Criação da tabela dimensional final, otimizada com **`ZORDER`** na coluna de data, para consumo ultrarrápido pelo Power BI.

### Restrições de Stack

* O processamento é feito exclusivamente com **Spark (PySpark/SQL)**.
* **Pandas** e tecnologias como **Microsoft Fabric** foram evitadas, conforme requisito do projeto, em favor de soluções nativas do Databricks/Spark.

---

##  Insights & O Modelo da Curva de Gauss

A principal inovação técnica está na Camada Silver:

### 1. Modelo de Volatilidade
O projeto calcula o **Desvio Padrão ($\sigma$)** da amplitude histórica (`máxima - mínima`) do Mini Dólar.

> **Conceito:** Usando a **Curva de Gauss**, a probabilidade estatística de o preço se mover dentro de $\pm 1\sigma$ (68,2% de chance) ou $\pm 2\sigma$ (95,4% de chance) pode ser usada para definir as regiões de Resistência e Suporte com maior precisão e fundamentação estatística.

### 2. Definição dos Níveis (Silver)
Os Níveis são definidos em múltiplos do Desvio Padrão ($\sigma$) em torno do preço de fechamento do dia:
* **Nível Central (Ponto de Pivô):** `Fechamento`
* **Resistência (R1, R2, R3):** `Fechamento + (N * σ)`
* **Suporte (S1, S2, S3):** `Fechamento - (N * σ)`

## Conexão e Entrega (Consulta Direta)

O Power BI conecta-se à Camada Gold via **DirectQuery**. Isso significa que cada vez que o usuário interage com o Slicer de data, o Power BI envia uma **consulta SQL direta ao Databricks**.

* O **DAX** é usado minimamente (apenas para filtros dinâmicos e formatação condicional de cores: verde para Suporte, vermelho para Resistência), garantindo que o relatório seja leve e não sofra com latência de dados desatualizados.

---

## Tech Stack

* **Cloud & Processing:** Databricks, Apache Spark (PySpark).
* **Storage:** Delta Lake (Unity Catalog).
* **Languages:** Python, SQL, DAX.
* **Visualization:** Microsoft Power BI.
