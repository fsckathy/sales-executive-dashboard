# Medidas DAX

Catálogo das **30 medidas** do modelo semântico do **Sales Executive Dashboard**, organizadas nas 9 pastas de exibição da tabela `_measures`.

> **Moeda:** os valores de origem estão em USD. As medidas com sufixo **BRL** convertem cada dia pela cotação USD/BRL do Banco Central (`dim_calendar[Taxa_USD_BRL]`). As medidas base em USD (`Receita`, `Lucro`, `Custo`) servem de insumo para as versões em BRL.

## Índice

| Pasta | Medida | Formato |
|---|---|---|
| [Pedidos](#pedidos) | [Pedidos](#pedidos-1) | Número inteiro |
|  | [Unidades Vendidas](#unidades-vendidas) | Número inteiro |
|  | [Frequência de Compra](#frequência-de-compra) | Número decimal |
| [Receita](#receita) | [Receita](#receita-1) | Moeda (USD) |
|  | [Ticket Médio](#ticket-médio) | Moeda (BRL) |
|  | [Receita MoM %](#receita-mom-) | Percentual |
|  | [Receita Clientes Recorrentes](#receita-clientes-recorrentes) | Moeda (BRL) |
|  | [Receita BRL](#receita-brl) | Moeda (BRL) |
|  | [Receita por Cliente BRL](#receita-por-cliente-brl) | Moeda (BRL) |
| [Lucro](#lucro) | [Lucro](#lucro-1) | Moeda (USD) |
|  | [Lucro por Pedido](#lucro-por-pedido) | Moeda (BRL) |
|  | [Lucro BRL](#lucro-brl) | Moeda (BRL) |
|  | [Lucro Top 10 Produtos BRL](#lucro-top-10-produtos-brl) | Moeda (BRL) |
| [Margem](#margem) | [Margem Bruta %](#margem-bruta-) | Percentual |
|  | [Margem Mês Anterior](#margem-mês-anterior) | Percentual |
|  | [Variação Margem pp](#variação-margem-pp) | Pontos percentuais |
| [Cliente](#cliente) | [Clientes Compradores](#clientes-compradores) | Número inteiro |
|  | [Clientes Recorrentes](#clientes-recorrentes) | Número inteiro |
|  | [Taxa de Clientes Recorrentes](#taxa-de-clientes-recorrentes) | Percentual |
| [Custo](#custo) | [Custo](#custo-1) | Moeda (USD) |
|  | [Custo BRL](#custo-brl) | Moeda (BRL) |
| [Participação](#participação) | [Participação Receita Categoria %](#participação-receita-categoria-) | Percentual |
|  | [Participação Receita Recorrentes %](#participação-receita-recorrentes-) | Percentual |
| [Executivo](#executivo) | [Exec Receita MoM](#exec-receita-mom) | Percentual |
|  | [Exec Receita Comparação](#exec-receita-comparação) | Texto |
|  | [Exec Lucro Contexto](#exec-lucro-contexto) | Texto |
|  | [Exec Margem Comparação](#exec-margem-comparação) | Texto |
|  | [Exec Ticket Contexto](#exec-ticket-contexto) | Texto |
| [Concentração](#concentração) | [Concentração Top 5 Produtos BRL %](#concentração-top-5-produtos-brl-) | Percentual |
|  | [Concentração Top 10 Clientes BRL %](#concentração-top-10-clientes-brl-) | Percentual |

## Pedidos

### Pedidos

**Formato:** Número inteiro `#,0`<br>
**Usada por:** [Frequência de Compra](#frequência-de-compra), [Lucro por Pedido](#lucro-por-pedido), [Receita Clientes Recorrentes](#receita-clientes-recorrentes), [Ticket Médio](#ticket-médio)

```dax
Pedidos = DISTINCTCOUNT(fact_sales[Order_ID])
```

### Unidades Vendidas

**Formato:** Número inteiro `#,0`

```dax
'Unidades Vendidas' = SUM(fact_sales[Quantity])
```

### Frequência de Compra

**Formato:** Número decimal `0.00`<br>
**Usa:** [Clientes Compradores](#clientes-compradores), [Pedidos](#pedidos-1)

```dax
'Frequência de Compra' = DIVIDE([Pedidos], [Clientes Compradores])
```

[Voltar ao índice](#índice)

## Receita

### Receita

**Formato:** Moeda (USD) `\$#,0.###############;(\$#,0.###############);\$#,0.###############`<br>
**Usada por:** [Receita BRL](#receita-brl)

```dax
Receita = SUM(fact_sales[Sales_Amount])
```

### Ticket Médio

**Formato:** Moeda (BRL) `"R$" #,0.00;("R$" #,0.00);"R$" #,0.00`<br>
**Usa:** [Pedidos](#pedidos-1), [Receita BRL](#receita-brl)<br>
**Usada por:** [Exec Ticket Contexto](#exec-ticket-contexto)

```dax
'Ticket Médio' = DIVIDE([Receita BRL], [Pedidos])
```

### Receita MoM %

**Formato:** Percentual `0.00%;-0.00%;0.00%`<br>
**Usa:** [Receita BRL](#receita-brl)<br>
**Usada por:** [Exec Receita MoM](#exec-receita-mom)

```dax
'Receita MoM %' =
    VAR ReceitaAtual = [Receita BRL]
    VAR ReceitaAnterior =
    CALCULATE(
        [Receita BRL],
        REMOVEFILTERS(dim_calendar),
        DATEADD('dim_calendar'[Date], -1, MONTH)
    )
    RETURN
    DIVIDE(ReceitaAtual-ReceitaAnterior, ReceitaAnterior)
```

### Receita Clientes Recorrentes

**Formato:** Moeda (BRL) `"R$"\ #,0.###############;-"R$"\ #,0.###############;"R$"\ #,0.###############`<br>
**Usa:** [Pedidos](#pedidos-1), [Receita BRL](#receita-brl)<br>
**Usada por:** [Participação Receita Recorrentes %](#participação-receita-recorrentes-)

```dax
'Receita Clientes Recorrentes' =
    VAR ClientesRecorrentes =
        FILTER(
            VALUES('Fact_Sales'[Customer_Key]),
            CALCULATE([Pedidos]) >= 2
        )
    RETURN
        CALCULATE(
            [Receita BRL],
            KEEPFILTERS(ClientesRecorrentes)
        )
```

### Receita BRL

**Formato:** Moeda (BRL) `"R$" #,0.00;("R$" #,0.00);"R$" #,0.00`<br>
**Usa:** [Receita](#receita-1)<br>
**Usada por:** [Concentração Top 10 Clientes BRL %](#concentração-top-10-clientes-brl-), [Concentração Top 5 Produtos BRL %](#concentração-top-5-produtos-brl-), [Margem Bruta %](#margem-bruta-), [Participação Receita Categoria %](#participação-receita-categoria-), [Participação Receita Recorrentes %](#participação-receita-recorrentes-), [Receita Clientes Recorrentes](#receita-clientes-recorrentes), [Receita MoM %](#receita-mom-), [Receita por Cliente BRL](#receita-por-cliente-brl), [Ticket Médio](#ticket-médio)

```dax
'Receita BRL' =
    SUMX(
        VALUES('dim_calendar'[Date_Key]),
        VAR ReceitaDia =
            CALCULATE([Receita])
        VAR TaxaDia =
            CALCULATE(
                SELECTEDVALUE('dim_calendar'[Taxa_USD_BRL])
            )
        RETURN
            IF(
                NOT ISBLANK(ReceitaDia),
                IF(
                    ISBLANK(TaxaDia),
                    ERROR("Existe uma data com vendas sem taxa de câmbio."),
                    ReceitaDia * TaxaDia
                )
            )
    )
```

### Receita por Cliente BRL

**Formato:** Moeda (BRL) `"R$" #,0.00;("R$" #,0.00);"R$" #,0.00`<br>
**Usa:** [Clientes Compradores](#clientes-compradores), [Receita BRL](#receita-brl)

```dax
'Receita por Cliente BRL' = DIVIDE([Receita BRL], [Clientes Compradores])
```

[Voltar ao índice](#índice)

## Lucro

### Lucro

**Formato:** Moeda (USD) `\$#,0.###############;(\$#,0.###############);\$#,0.###############`<br>
**Usada por:** [Lucro BRL](#lucro-brl)

```dax
Lucro = SUM(fact_sales[Profit])
```

### Lucro por Pedido

**Formato:** Moeda (BRL) `"R$" #,0.00;("R$" #,0.00);"R$" #,0.00`<br>
**Usa:** [Lucro BRL](#lucro-brl), [Pedidos](#pedidos-1)<br>
**Usada por:** [Exec Lucro Contexto](#exec-lucro-contexto)

```dax
'Lucro por Pedido' = DIVIDE([Lucro BRL], [Pedidos])
```

### Lucro BRL

**Formato:** Moeda (BRL) `"R$" #,0.00;("R$" #,0.00);"R$" #,0.00`<br>
**Usa:** [Lucro](#lucro-1)<br>
**Usada por:** [Lucro Top 10 Produtos BRL](#lucro-top-10-produtos-brl), [Lucro por Pedido](#lucro-por-pedido), [Margem Bruta %](#margem-bruta-)

```dax
'Lucro BRL' =
    SUMX(
        VALUES('dim_calendar'[Date_Key]),
        VAR LucroDia =
            CALCULATE([Lucro])
        VAR TaxaDia =
            CALCULATE(
                SELECTEDVALUE('dim_calendar'[Taxa_USD_BRL])
            )
        RETURN
            IF(
                NOT ISBLANK(LucroDia),
                IF(
                    ISBLANK(TaxaDia),
                    ERROR("Existe uma data com vendas sem taxa de câmbio."),
                    LucroDia * TaxaDia
                )
            )
    )
```

### Lucro Top 10 Produtos BRL

**Formato:** Moeda (BRL) `"R$" #,0.00;("R$" #,0.00);"R$" #,0.00`<br>
**Usa:** [Lucro BRL](#lucro-brl)

```dax
'Lucro Top 10 Produtos BRL' =
    VAR Universo =
        CALCULATETABLE(
            ADDCOLUMNS(VALUES(dim_product[Product_Key]), "@Lucro", [Lucro BRL]),
            ALLSELECTED(dim_product)
        )
    VAR Lideres = TOPN(10, Universo, [@Lucro], DESC, dim_product[Product_Key], ASC)
    VAR ProdutoAtual = SELECTEDVALUE(dim_product[Product_Key])
    RETURN IF(CONTAINS(Lideres, dim_product[Product_Key], ProdutoAtual), [Lucro BRL])
```

[Voltar ao índice](#índice)

## Margem

### Margem Bruta %

**Formato:** Percentual `0.00%;-0.00%;0.00%`<br>
**Usa:** [Lucro BRL](#lucro-brl), [Receita BRL](#receita-brl)<br>
**Usada por:** [Exec Margem Comparação](#exec-margem-comparação), [Margem Mês Anterior](#margem-mês-anterior), [Variação Margem pp](#variação-margem-pp)

```dax
'Margem Bruta %' = DIVIDE([Lucro BRL], [Receita BRL])
```

### Margem Mês Anterior

**Formato:** Percentual `0.00%;-0.00%;0.00%`<br>
**Usa:** [Margem Bruta %](#margem-bruta-)<br>
**Usada por:** [Variação Margem pp](#variação-margem-pp)

```dax
'Margem Mês Anterior' =
    VAR DatasAnteriores = DATEADD(dim_calendar[Date], -1, MONTH)
    RETURN
    CALCULATE(
    [Margem Bruta %],
    REMOVEFILTERS(dim_calendar),
    DatasAnteriores
    )
```

### Variação Margem pp

**Formato:** Pontos percentuais `0.00 "pp"`<br>
**Usa:** [Margem Bruta %](#margem-bruta-), [Margem Mês Anterior](#margem-mês-anterior)

```dax
'Variação Margem pp' =
    VAR MargemAnterior = [Margem Mês Anterior]
    RETURN
        IF(
            NOT ISBLANK([Margem Bruta %])
                && NOT ISBLANK(MargemAnterior),
            ([Margem Bruta %] - MargemAnterior) * 100
        )
```

[Voltar ao índice](#índice)

## Cliente

### Clientes Compradores

**Formato:** Número inteiro `#,0`<br>
**Usada por:** [Frequência de Compra](#frequência-de-compra), [Receita por Cliente BRL](#receita-por-cliente-brl), [Taxa de Clientes Recorrentes](#taxa-de-clientes-recorrentes)

```dax
'Clientes Compradores' = DISTINCTCOUNT(fact_sales[Customer_Key])
```

### Clientes Recorrentes

**Formato:** Número inteiro `#,0`<br>
**Usada por:** [Taxa de Clientes Recorrentes](#taxa-de-clientes-recorrentes)

```dax
'Clientes Recorrentes' =
    COUNTROWS(
        FILTER(
            VALUES('Fact_Sales'[Customer_Key]),
            CALCULATE(
                DISTINCTCOUNT('Fact_Sales'[Order_ID])
            ) >= 2
        )
    )
```

### Taxa de Clientes Recorrentes

**Formato:** Percentual `0.00%;-0.00%;0.00%`<br>
**Usa:** [Clientes Compradores](#clientes-compradores), [Clientes Recorrentes](#clientes-recorrentes)

```dax
'Taxa de Clientes Recorrentes' =
    DIVIDE(
        [Clientes Recorrentes],
        [Clientes Compradores]
    )
```

[Voltar ao índice](#índice)

## Custo

### Custo

**Formato:** Moeda (USD) `\$#,0.###############;(\$#,0.###############);\$#,0.###############`<br>
**Usada por:** [Custo BRL](#custo-brl)

```dax
Custo = sum(fact_sales[Cost_Amount])
```

### Custo BRL

**Formato:** Moeda (BRL) `"R$" #,0.00;("R$" #,0.00);"R$" #,0.00`<br>
**Usa:** [Custo](#custo-1)

```dax
'Custo BRL' =
    SUMX(
        VALUES('dim_calendar'[Date_Key]),
        VAR CustoDia =
            CALCULATE([Custo])
        VAR TaxaDia =
            CALCULATE(
                SELECTEDVALUE('dim_calendar'[Taxa_USD_BRL])
            )
        RETURN
            IF(
                NOT ISBLANK(CustoDia),
                IF(
                    ISBLANK(TaxaDia),
                    ERROR("Existe uma data com vendas sem taxa de câmbio."),
                    CustoDia * TaxaDia
                )
            )
    )
```

[Voltar ao índice](#índice)

## Participação

### Participação Receita Categoria %

**Formato:** Percentual `0.00%;-0.00%;0.00%`<br>
**Usa:** [Receita BRL](#receita-brl)

```dax
'Participação Receita Categoria %' =
    DIVIDE(
        [Receita BRL],
        CALCULATE(
            [Receita BRL],
            ALLSELECTED(dim_product[Categoria])
        )
    )
```

### Participação Receita Recorrentes %

**Formato:** Percentual `0.00%;-0.00%;0.00%`<br>
**Usa:** [Receita BRL](#receita-brl), [Receita Clientes Recorrentes](#receita-clientes-recorrentes)

```dax
'Participação Receita Recorrentes %' =
    DIVIDE(
        [Receita Clientes Recorrentes],
        [Receita BRL]
    )
```

[Voltar ao índice](#índice)

## Executivo

### Exec Receita MoM

**Formato:** Percentual `0.00%;-0.00%;0.00%`<br>
**Usa:** [Receita MoM %](#receita-mom-)<br>
**Usada por:** [Exec Receita Comparação](#exec-receita-comparação)

```dax
'Exec Receita MoM' =
    IF(
    HASONEVALUE(dim_calendar[Year_Month]), [Receita MoM %]
    )
```

### Exec Receita Comparação

**Formato:** Texto dinâmico<br>
**Usa:** [Exec Receita MoM](#exec-receita-mom)

```dax
'Exec Receita Comparação' =
    VAR V = [Exec Receita MoM]
    RETURN
    IF(
        ISBLANK(V),
        "Selecione um mês com base comparável",
        FORMAT(V, "+0.0%;-0.0%;0.0%") & " vs. mês anterior"
        )
```

### Exec Lucro Contexto

**Formato:** Texto dinâmico<br>
**Usa:** [Lucro por Pedido](#lucro-por-pedido)

```dax
'Exec Lucro Contexto' = "Por pedido: " & FORMAT([Lucro por Pedido], "R$ #,0.00", "pt-BR")
```

### Exec Margem Comparação

**Formato:** Texto dinâmico<br>
**Usa:** [Margem Bruta %](#margem-bruta-)

```dax
'Exec Margem Comparação' =
    VAR MesUnico = HASONEVALUE(dim_calendar[Year_Month])
    VAR DataReferencia = MAX(dim_calendar[Date])
    VAR InicioMes = DATE(YEAR(DataReferencia), MONTH(DataReferencia), 1)
    VAR FimMes = EOMONTH(DataReferencia, 0)
    VAR InicioAnterior = EDATE(InicioMes, -1)
    VAR FimAnterior = InicioMes - 1
    VAR MargemAtual =
        CALCULATE(
            [Margem Bruta %],
            FILTER(ALL(dim_calendar), dim_calendar[Date] >= InicioMes && dim_calendar[Date] <= FimMes)
        )
    VAR MargemAnterior =
        CALCULATE(
            [Margem Bruta %],
            FILTER(ALL(dim_calendar), dim_calendar[Date] >= InicioAnterior && dim_calendar[Date] <= FimAnterior)
        )
    VAR Variacao = (MargemAtual - MargemAnterior) * 100
    RETURN
        SWITCH(
            TRUE(),
            NOT MesUnico, "Selecione um único mês",
            ISBLANK(MargemAtual), "Sem vendas no mês selecionado",
            ISBLANK(MargemAnterior), "Sem base no mês anterior",
            FORMAT(Variacao, "+0.00;-0.00;0.00", "pt-BR") & " p.p. vs. mês anterior"
        )
```

### Exec Ticket Contexto

**Formato:** Texto dinâmico<br>
**Usa:** [Ticket Médio](#ticket-médio)

```dax
'Exec Ticket Contexto' = "Ticket médio: " & FORMAT([Ticket Médio], "R$ #,0.00", "pt-BR")
```

[Voltar ao índice](#índice)

## Concentração

### Concentração Top 5 Produtos BRL %

**Formato:** Percentual `0.0%`<br>
**Usa:** [Receita BRL](#receita-brl)

```dax
'Concentração Top 5 Produtos BRL %' =
    VAR Universo =
        CALCULATETABLE(
            ADDCOLUMNS(VALUES(dim_product[Product_Key]), "@Receita", [Receita BRL]),
            ALLSELECTED(dim_product)
        )
    VAR Lideres = TOPN(5, Universo, [@Receita], DESC, dim_product[Product_Key], ASC)
    RETURN DIVIDE(
        SUMX(Lideres, [@Receita]),
        CALCULATE([Receita BRL], ALLSELECTED(dim_product))
    )
```

### Concentração Top 10 Clientes BRL %

**Formato:** Percentual `0.00%`<br>
**Usa:** [Receita BRL](#receita-brl)

```dax
'Concentração Top 10 Clientes BRL %' =
    VAR Universo =
        CALCULATETABLE(
            ADDCOLUMNS(VALUES(fact_sales[Customer_Key]), "@Receita", [Receita BRL]),
            ALLSELECTED(fact_sales)
        )
    VAR Lideres = TOPN(10, Universo, [@Receita], DESC, fact_sales[Customer_Key], ASC)
    RETURN DIVIDE(
        SUMX(Lideres, [@Receita]),
        CALCULATE([Receita BRL], ALLSELECTED(fact_sales))
    )
```

[Voltar ao índice](#índice)
