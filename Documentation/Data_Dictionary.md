# Dicionário de Dados

Colunas visíveis do modelo semântico do **Sales Executive Dashboard** (Power BI), com descrição e tipo de dado. Chaves técnicas e colunas auxiliares ocultas não estão listadas.

## Modelo

Esquema estrela: uma tabela fato de vendas ligada a quatro dimensões.

| Tabela | Tipo | Colunas documentadas | Descrição |
|---|---|:---:|---|
| [`fact_sales`](#fact_sales) | Fato | 1 | Vendas: uma linha por pedido, com quantidade, receita, custo e lucro em USD. |
| [`dim_calendar`](#dim_calendar) | Dimensão | 7 | Calendário diário, com a cotação USD/BRL do Banco Central (dias sem cotação usam a última disponível). |
| [`dim_product`](#dim_product) | Dimensão | 3 | Produtos, categorias e marcas, com nomes traduzidos para português. |
| [`dim_customer`](#dim_customer) | Dimensão | 3 | Clientes, com gênero, idade e segmento. |
| [`dim_geography`](#dim_geography) | Dimensão | 2 | Cidades e regiões das vendas. |

## Relacionamentos

Todos são muitos-para-um (fato → dimensão), com filtro em direção única.

| Da tabela fato | Para a dimensão |
|---|---|
| `fact_sales[Date_Key]` | `dim_calendar[Date_Key]` |
| `fact_sales[Product_Key]` | `dim_product[Product_Key]` |
| `fact_sales[Customer_Key]` | `dim_customer[Customer_Key]` |
| `fact_sales[Geography_Key]` | `dim_geography[Geography_Key]` |

## Tabelas

### fact_sales

*Fato* — Vendas: uma linha por pedido, com quantidade, receita, custo e lucro em USD.

| Coluna | Tipo de dado | Descrição |
|---|---|---|
| `Order_ID` | Texto | Identificador único do pedido. |

[Voltar ao topo](#dicionário-de-dados)

### dim_calendar

*Dimensão* — Calendário diário, com a cotação USD/BRL do Banco Central (dias sem cotação usam a última disponível).

| Coluna | Tipo de dado | Descrição |
|---|---|---|
| `Date` | Data/Hora | Data do calendário baseada no período de vendas da fact_table. |
| `Year` | Número Inteiro | Ano correspondente à data do calendário. |
| `Month` | Número Inteiro | Valor numérico correspondente ao mês da data, de 1 a 12. |
| `Month_Name` | Texto | Nome do mês correspondente à data no calendário. |
| `Quarter` | Texto | Trimestre correspondente à data, identificado como Q1, Q2, Q3 ou Q4. |
| `Year_Month` | Texto | Ano e mês correspondentes à data, no formato YY-MM. |
| `Taxa_USD_BRL` | Moeda | Taxa de câmbio do dólar americano (USD) em relação ao real brasileiro (BRL) na data correspondente. |

[Voltar ao topo](#dicionário-de-dados)

### dim_product

*Dimensão* — Produtos, categorias e marcas, com nomes traduzidos para português.

| Coluna | Tipo de dado | Descrição |
|---|---|---|
| `Brand` | Texto | Marca associada ao produto. |
| `Categoria` | Texto | Categoria à qual o produto pertence, em português. |
| `Produto` | Texto | Nome do produto, em português. |

[Voltar ao topo](#dicionário-de-dados)

### dim_customer

*Dimensão* — Clientes, com gênero, idade e segmento.

| Coluna | Tipo de dado | Descrição |
|---|---|---|
| `Age` | Número Inteiro | Idade do cliente no momento do registro. |
| `Gênero` | Texto | Gênero do cliente em português. |
| `Segmento` | Texto | Segmento ao qual o cliente pertence, em português. |

[Voltar ao topo](#dicionário-de-dados)

### dim_geography

*Dimensão* — Cidades e regiões das vendas.

| Coluna | Tipo de dado | Descrição |
|---|---|---|
| `City` | Texto | Cidade associada à localização da venda. |
| `Região` | Texto | Região geográfica associada à localização, em português. |

[Voltar ao topo](#dicionário-de-dados)
