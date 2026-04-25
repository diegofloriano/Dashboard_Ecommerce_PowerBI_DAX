# Desafio de Projeto: Modelagem e Transformação de Dados com DAX no Power BI

Este repositório contém a solução para o desafio de projeto focado na transformação de uma base de dados única (flat table) em um modelo dimensional **Star Schema** (Esquema em Estrela). O objetivo principal é otimizar a performance e a organização dos dados para facilitar a análise e a criação de relatórios.

## 🛠️ Ferramentas Utilizadas
* **Power BI Desktop**
* **Power Query** (Linguagem M)
* **DAX** (Data Analysis Expressions)

---

## 📈 Processo de Construção do Modelo

O processo foi dividido em três etapas principais: Extração/Transformação (Power Query), Criação de Inteligência de Tempo (DAX) e Modelagem (Relacionamentos).

### 1. Preparação e Backup
* **financials_origem**: Realizei a carga da base de dados original e criei uma cópia de segurança. Esta tabela foi definida como "oculta" no modelo e a sua carga foi desabilitada para o relatório, servindo apenas como referência de backup.

### 2. Criação das Tabelas Dimensão (D_)
As tabelas dimensão foram criadas a partir da tabela original, selecionando as colunas pertinentes para reduzir a redundância:

* **D_Produtos**: Criada através da funcionalidade "Agrupar por" no Power Query. As agregações incluídas foram:
    * Soma das Unidades Vendidas
    * Média do valor de vendas
    * Mediana do valor de vendas
    * Valor máximo de Venda
    * Valor mínimo de Venda
* **D_Produtos_Detalhes**: Contém informações específicas de fabricação e preços unitários (`ID_produtos`, `Discount Band`, `Sale Price`, `Units Sold`, `Manufacturing Price`).
* **D_Descontos**: Focada na relação entre produtos e suas respectivas faixas de desconto (`ID_produto`, `Discount`, `Discount Band`).
* **D_Detalhes**: Tabela complementar com informações adicionais de vendas não contempladas nas outras dimensões.
* **D_Calendário**: Criada utilizando a função DAX `CALENDAR()`. Esta tabela é fundamental para garantir a integridade das análises temporais (Time Intelligence).

### 3. Criação da Tabela Fato (F_)
* **F_Vendas**: É a tabela central do modelo, contendo as métricas quantitativas e as chaves estrangeiras.
    * Foi criada uma coluna **SK_ID** (Surrogate Key) para identificação única dos registros.
    * Colunas mantidas: `SK_ID`, `ID_Produto`, `Produto`, `Units Sold`, `Sales Price`, `Discount Band`, `Segment`, `Country`, `Salers`, `Profit` e os campos de data.

---

## 🔢 Funções DAX Utilizadas

Para a tabela de calendário, utilizei a seguinte lógica para abranger todo o período de vendas presente na tabela fato:

```dax
D_Calendário = CALENDAR(
    MIN(F_Vendas[Date]), 
    MAX(F_Vendas[Date])
)
