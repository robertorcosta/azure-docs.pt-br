---
title: Práticas recomendadas para usar o Power BI para consultar e visualizar dados do Azure Data Explorer
description: Neste artigo, você aprende as melhores práticas para usar o Power BI para consultar e visualizar dados do Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251733"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Práticas recomendadas para usar o Power BI para consultar e visualizar dados do Azure Data Explorer

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. [Power BI](https://docs.microsoft.com/power-bi/) é uma solução de análise de negócios que permite visualizar seus dados e compartilhar os resultados em toda a sua organização. O Azure Data Explorer oferece três opções para se conectar a dados no Power BI. Use o [conector incorporado,](power-bi-connector.md) [importe uma consulta do Azure Data Explorer para o Power BI](power-bi-imported-query.md)ou use uma consulta [SQL](power-bi-sql-query.md). Este artigo fornece dicas para consultar e visualizar seus dados do Azure Data Explorer com o Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Melhores práticas para usar power bi 

Ao trabalhar com terabytes de dados brutos frescos, siga estas diretrizes para manter os dashboards de Power BI e relatórios snappy e atualizados:

* **Luz de viagem** - Traga apenas os dados necessários para seus relatórios ao Power BI. Para análise interativa profunda, use a [UI do Azure Data Explorer](web-query-data.md) que é otimizada para exploração ad-hoc com a Linguagem de Consulta Kusto.

* **Modelo composto** - Use [modelo composto](https://docs.microsoft.com/power-bi/desktop-composite-models) para combinar dados agregados para painéis de alto nível com dados brutos operacionais filtrados. Você pode definir claramente quando usar dados brutos e quando usar uma exibição agregada. 

* **Modo de importação versus modo DirectQuery** - Use o modo **Importação** para interação de conjuntos de dados menores. Use o modo **DirectQuery** para conjuntos de dados grandes e frequentemente atualizados. Por exemplo, crie tabelas de dimensões usando o modo **Importação,** uma vez que são pequenas e não mudam com frequência. Defina o intervalo de atualização de acordo com a taxa esperada de atualizações de dados. Crie tabelas de fatos usando o modo **DirectQuery,** uma vez que essas tabelas são grandes e contêm dados brutos. Use essas tabelas para apresentar dados filtrados usando [a broca](https://docs.microsoft.com/power-bi/desktop-drillthrough)Power BI through .

* **Paralelismo** – O Azure Data Explorer é uma plataforma de dados linearmente escalável, portanto, você pode melhorar o desempenho da renderização do painel aumentando o paralelismo do fluxo de ponta a ponta da seguinte forma:

   * Aumente o número de [conexões simultâneas no DirectQuery in Power BI](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * Use [consistência fraca para melhorar o paralelismo.](/azure/kusto/concepts/queryconsistency) Isso pode ter um impacto no frescor dos dados.

* **Fatiadores eficazes** – Use [fatiadores de sincronização](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) para evitar que os relatórios sejam carregados antes de estar pronto. Depois de estruturar o conjunto de dados, colocar todos os visuais e marcar todos os cortadores, você pode selecionar o cortador de sincronização para carregar apenas os dados necessários.

* **Use filtros** - Use o máximo de filtros power bi possível para concentrar a pesquisa do Azure Data Explorer nos fragmentos de dados relevantes.

* **Visuais eficientes** – Selecione os visuais mais performáticos para seus dados.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Dicas para usar o conector Azure Data Explorer para Power BI para consultar dados

A seção a seguir inclui dicas e truques para usar o idioma de consulta Kusto com Power BI. Use o [conector Azure Data Explorer para Power BI](power-bi-connector.md) para visualizar dados

### <a name="complex-queries-in-power-bi"></a>Consultas complexas no Power BI

Consultas complexas são mais facilmente expressas em Kusto do que em Power Query. Eles devem ser implementados como [funções kusto](/azure/kusto/query/functions), e invocados no Power BI. Este método é necessário ao `let` usar **directQuery** com instruções em sua consulta Kusto. Como o Power BI junta `let` duas consultas e as `join` declarações não podem ser usadas com o operador, erros de sintaxe podem ocorrer. Portanto, salve cada parte da junta como uma função Kusto e permita que o Power BI junte essas duas funções juntas.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Como simular um operador de data-hora relativa

O BI de energia não contém um `ago()`operador de data-hora *relativo,* como .
Para `ago()`simular, use `DateTime.FixedLocalNow()` `#duration` uma combinação de funções power bi.

Em vez desta consulta `ago()` usando o operador:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Use a seguinte consulta equivalente:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Atingindo os limites de consulta de Kusto 

As consultas kusto retornam, por padrão, até 500.000 linhas ou 64 MB, conforme descrito nos [limites de consulta](/azure/kusto/concepts/querylimits). Você pode substituir esses padrões usando **opções avançadas** na janela de conexão **Do Azure Data Explorer (Kusto):**

![opções avançadas](media/power-bi-best-practices/advanced-options.png)

Essas opções [emitem instruções definidas](/azure/kusto/query/setstatement) com sua consulta para alterar os limites de consulta padrão:

  * **O número de registro de resultado da consulta de** limite gera um`set truncationmaxrecords`
  * **Limitar o tamanho dos dados de resultado de consulta no Bytes** gera um`set truncationmaxsize`
  * Desativar a **truncação definida por resultados** gera um`set notruncation`

### <a name="using-query-parameters"></a>Usando parâmetros de consulta

Você pode usar [parâmetros de consulta](/azure/kusto/query/queryparametersstatement) para modificar sua consulta dinamicamente. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Usando um parâmetro de consulta nos detalhes da conexão

Use um parâmetro de consulta para filtrar informações na consulta e otimizar o desempenho da consulta.
 
Na janela **Editar consultas,** **home** > **advanced editor**

1. Encontre a seguinte seção da consulta:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Por exemplo: 

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Substitua a parte relevante da consulta pelo seu parâmetro. Divida a consulta em várias partes e concatena-as de volta usando uma ampersand (&), juntamente com o parâmetro.

   Por exemplo, na consulta acima, pegaremos `State == 'ALABAMA'` a parte e `State == '` `'` dividiremos para: `State` e colocaremos o parâmetro entre eles:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Se a sua consulta contiver aspas, codifique-as corretamente. Por exemplo, a seguinte consulta: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   aparecerá no **Editor Avançado** da seguinte forma com duas aspas:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Deve ser substituído pela seguinte consulta com três aspas:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Use um parâmetro de consulta nas etapas de consulta

Você pode usar um parâmetro de consulta em qualquer etapa de consulta que o suporte. Por exemplo, filtre os resultados com base no valor de um parâmetro.

![resultados do filtro usando um parâmetro](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Não use o agendador de atualização de dados do Power BI para emitir comandos de controle para o Kusto

O Power BI inclui um agendador de atualização de dados que pode emitir periodicamente consultas contra uma fonte de dados. Esse mecanismo não deve ser usado para agendar comandos de controle para o Kusto porque o Power BI assume que todas as consultas são somente leitura.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI pode enviar&lt;apenas consultas curtas (2000 caracteres) para Kusto

Se a execução de uma consulta no Power BI resultar no seguinte erro: _"DataSource.Error: Web.Contents failed to get contents from..."_ A consulta provavelmente tem mais de 2000 caracteres. O Power BI usa **o PowerQuery** para consultar o Kusto, emitindo uma solicitação HTTP GET que codifica a consulta como parte do URI que está sendo recuperado. Portanto, as consultas kusto emitidas pelo Power BI estão limitadas ao comprimento máximo de um URI de solicitação (2000 caracteres, menos pequena compensação). Como solução de solução, você pode definir uma [função armazenada](/azure/kusto/query/schema-entities/stored-functions) no Kusto e fazer com que o Power BI use essa função na consulta.

## <a name="next-steps"></a>Próximas etapas

[Visualize dados usando o conector do Azure Data Explorer para Power BI](power-bi-connector.md)




