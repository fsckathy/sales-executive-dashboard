|Tabela         |Coluna              |Descrição                                                                                                     |Tipo de Dado   |
|---------------|--------------------|--------------------------------------------------------------------------------------------------------------|---------------|
|fact_sales     |Order_ID            |Identificador único do pedido.                                                                                |Texto          |
|fact_sales     |Date_Key            |Chave de identificação da data da venda, utilizada para conectar à dim_calendar.                              |Número Inteiro          |
|fact_sales     |Customer_Key        |Chave de identificação do cliente relacionado à venda, utilizada para conectar à dim_customer.                |Texto          |
|fact_sales     |Product_Key         |Chave de identificação do produto vendido, utilizada para conectar à dim_product.                             |Texto          |
|fact_sales     |Geography_Key       |Chave de identificação da localização geográfica da venda, utilizada para conectar à dim_geography.           |Texto          |
|fact_sales     |Quantity            |Quantidade de unidades vendidas.                                                                              |Número Inteiro |
|fact_sales     |Sales_Amount        |Valor total da venda.                                                                                         |Moeda          |
|fact_sales     |Cost_Amount         |Valor total dos custos associados à venda.                                                                    |Moeda          |
|fact_sales     |Profit              |Lucro obtido com a venda.                                                                                     |Moeda          |
|dim_product    |Product_Key         |Chave única de identificação do produto, utilizada para relacionar à tabela fact_sales.                       |Texto          |
|dim_product    |Product_Name        |Nome do produto, em inglês.                                                                                   |Texto          |
|dim_product    |Category            |Categoria à qual o produto pertence, em inglês.                                                               |Texto          |
|dim_product    |Brand               |Marca associada ao produto.                                                                                   |Texto          |
|dim_product    |Categoria           |Categoria à qual o produto pertence, em português.                                                            |Texto          |
|dim_product    |Produto             |Nome do produto, em português.                                                                                |Texto          |
|dim_geography  |Geography_Key       |Chave única de identificação da localização geográfica, utilizada para relacionar à tabela fact_sales.        |Texto          |
|dim_geography  |Region              |Região geográfica associada à localização, em inglês.                                                         |Texto          |
|dim_geography  |City                |Cidade associada à localização do cliente.                                                                    |Texto          |
|dim_geography  |Região              |Região geográfica associada à localização, em português.                                                      |Texto          |
|dim_calendar   |Date_Key            |Chave única da data no calendário, utilizada para relacionar a dim_calendar à tabela fact_sales.              |Número Inteiro |
|dim_calendar   |Date                |Data do calendário baseada no período de vendas da fact_table.                                                |Data/Hora      |
|dim_calendar   |Year                |Ano correspondente à data do calendário.                                                                      |Número Inteiro |
|dim_calendar   |Month               |Valor numérico correspondente ao mês da data, de 1 a 12.                                                      |Número Inteiro |
|dim_calendar   |Month_Name          |Nome do mês correspondente à data no calendário.                                                              |Texto          |
|dim_calendar   |Quarter             |Trimestre correspondente à data, identificado como Q1, Q2, Q3 ou Q4.                                          |Texto          |
|dim_calendar   |Year_Month          |Ano e mês correspondentes à data, no formato YY-MM.                                                           |Texto          |
|dim_calendar   |Taxa_USD_BRL        |Taxa de câmbio do dólar americano (USD) em relação ao real brasileiro (BRL) na data correspondente.           |Moeda          |
|dim_customer   |Customer_Key        |Chave única de identificação do cliente, utilizada para relacionar à tabela fact_sales.                       |Texto          |
|dim_customer   |Gender              |Gênero do cliente em inglês.                                                                                  |Texto          |
|dim_customer   |Age                 |Idade do cliente no momento do registro.                                                                      |Texto          |
|dim_customer   |Segment             |Segmento ao qual o cliente pertence, em inglês.                                                               |Texto          |
|dim_customer   |Gênero              |Gênero do cliente em português.                                                                               |Texto          |
|dim_customer   |Segmento            |Segmento ao qual o cliente pertence, em português.                                                            |Texto          |
