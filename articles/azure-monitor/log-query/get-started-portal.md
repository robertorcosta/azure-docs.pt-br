---
title: 'Tutorial: Introdução às consultas no Log Analytics'
description: Aprenda com este tutorial como escrever e gerenciar consultas de log do Azure Monitor usando o Log Analytics no portal do Azure.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 8917c73474b81ee76e6acdf8c6f274e962ee1215
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874224"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Tutorial: Introdução às consultas no Log Analytics

Este tutorial mostra a você como usar o Log Analytics para escrever, executar e gerenciar consultas de log do Azure Monitor no portal do Azure. Você pode usar as consultas do Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base em seus dados. 

Neste tutorial, você aprenderá a usar o Log Analytics para:

> [!div class="checklist"]
> * Entender o esquema de dados de log
> * Gravar e executar consultas simples e modificar o intervalo de tempo para consultas
> * Filtrar, classificar e agrupar resultados de consulta
> * Exibir, modificar e compartilhar visuais de resultados de consulta
> * Salvar, carregar, exportar e copiar consultas e resultados

Para obter mais informações sobre consultas de log, confira [Visão geral de consultas de log no Azure Monitor](log-query-overview.md).<br/>
Para obter um tutorial detalhado sobre como escrever consultas de log, confira [Introdução às consultas de log no Azure Monitor](get-started-queries.md).

## <a name="open-log-analytics"></a>Abrir Log Analytics
Para usar o Log Analytics, você precisa estar conectado a uma conta do Azure. Se você ainda não tiver uma conta do Azure, poderá [criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Para concluir a maioria das etapas deste tutorial, você pode usar [este ambiente de demonstração](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), que inclui muitos dados de exemplo. Com o ambiente de demonstração, você não poderá salvar consultas nem fixar resultados em um painel.

Se estiver usando o Azure Monitor para coletar dados de log em pelo menos um recurso do Azure, você também poderá usar seu próprio ambiente. Para abrir um workspace do Log Analytics, na navegação à esquerda do Azure Monitor, selecione **Logs**. 

## <a name="understand-the-schema"></a>Entenda o esquema
 
Um *esquema* é uma coleção de tabelas agrupadas em categorias lógicas. O esquema de demonstração tem várias categorias de monitoramento de soluções. Por exemplo, a categoria **LogManagement** contém eventos do Windows e do Syslog, dados de desempenho e pulsações do agente.

O esquema de tabela aparece na guia **Tabelas** do workspace do Log Analytics. As tabelas contêm colunas, cada uma com um tipo de dados mostrado pelo ícone ao lado do nome da coluna. Por exemplo, a tabela **Evento** contém colunas de texto como **Computador** e colunas numéricas como **EventCategory**.

![Esquema](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Escrever e executar consultas básicas

O Log Analytics abre com uma nova consulta em branco no **Editor de consultas**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Escreva uma consulta

As consultas de log do Azure Monitor usam uma versão da linguagem de consulta Kusto. As consultas podem começar com um nome de tabela ou um comando [search](/azure/kusto/query/searchoperator). 

A consulta a seguir recupera todos os registros da tabela **Evento**:

```Kusto
Event
```

O caractere de barra vertical (|) separa os comandos, portanto, a saída do primeiro comando é a entrada do próximo comando. Você pode adicionar qualquer número de comandos para uma única consulta. A consulta a seguir recupera os registros da tabela **Evento**, depois pesquisa pelo termo **erro** nesses registros, em qualquer propriedade:

```Kusto
Event 
| search "error"
```

Uma quebra de linha única torna as consultas mais fáceis de ler. Mais do que uma quebra de linha divide a consulta em consultas separadas.

Uma outra maneira de escrever a mesma consulta é:

```Kusto
search in (Event) "error"
```

No segundo exemplo, o comando **search** pesquisa pelo termo **erro** somente nos registros na tabela **Eventos**.

Por padrão, o Log Analytics limita as consultas a um intervalo de tempo das últimas 24 horas. Para definir um intervalo de tempo diferente, você pode adicionar um filtro **TimeGenerated** explícito à consulta ou usar o controle **Intervalo de tempo**.

### <a name="use-the-time-range-control"></a>Usar o controle Intervalo de tempo
Para usar o controle **Intervalo de tempo**, selecione-o na barra superior e, em seguida, selecione um valor na lista suspensa ou selecione **Personalizado** para criar um intervalo de tempo personalizado.

![Seletor de tempo](media/get-started-portal/time-picker.png)

- Os valores de intervalo de tempo selecionados estão em UTC, o que pode ser diferente do fuso horário local.
- Se a consulta definir explicitamente um filtro para **TimeGenerated**, o controle seletor de tempo mostrará **Definido na consulta** e será desabilitado para evitar um conflito.

### <a name="run-a-query"></a>Executar uma consulta
Para executar uma consulta, coloque o cursor em algum lugar dentro da consulta e selecione **Executar** na barra superior ou pressione **Shift**+**Enter**. A consulta é executada até ela encontrar uma linha em branco.

## <a name="filter-results"></a>Resultados do filtro
O Log Analytics limita os resultados a um máximo de 10.000 registros. Uma consulta geral como `Event` retorna resultados demais, impedindo-a de ser útil. Você pode filtrar os resultados por meio dos elementos da tabela ou então adicionando explicitamente um filtro à consulta. A filtragem por meio dos elementos da tabela retorna um novo conjunto de resultados, enquanto um filtro explícito se aplica ao conjunto de resultados existente.

### <a name="filter-by-restricting-table-elements"></a>Filtrar restringindo elementos de tabela
Para filtrar `Event` resultados da consulta para eventos de **Erro** restringindo os elementos da tabela na consulta:

1. Nos resultados da consulta, selecione a seta de lista suspensa ao lado de qualquer registro que tenha **Erro** na coluna **EventLevelName**. 
   
1. Nos detalhes expandidos, passe o mouse sobre e selecione **...** ao lado de **EventLevelName** e, em seguida, selecione **Incluir "Erro"** . 
   
   ![Adicionar um filtro à consulta](media/get-started-portal/add-filter.png)
   
1. Observe que a consulta no **Editor de consultas** foi alterada para:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Selecione **Executar** para executar a nova consulta.

### <a name="filter-by-explicitly-filtering-results"></a>Filtrar filtrando explicitamente os resultados
Para filtrar os resultados da consulta `Event` para eventos de **Erro** filtrando os resultados da consulta:

1. Nos resultados da consulta, selecione o ícone **Filtrar** ao lado do título de coluna **EventLevelName**. 
   
1. No primeiro campo da janela pop-up, selecione **É igual a** e, no próximo campo, insira *erro*. 
   
1. Selecione **Filtrar**.
   
   ![Filtrar](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Classificar, agrupar e selecionar colunas
Para classificar os resultados da consulta por uma coluna específica, como **TimeGenerated [UTC]** , selecione o título de coluna. Selecione o título novamente para alternar entre ordem crescente e decrescente.

![Coluna de classificação](media/get-started-portal/sort-column.png)

Outra maneira de organizar os resultados é por grupos. Para agrupar os resultados por uma coluna específica, arraste o cabeçalho da coluna para a barra acima da tabela de resultados rotulada **Arraste um cabeçalho de coluna e solte-o aqui para agrupar por essa coluna**. Para criar subgrupos, arraste outras colunas para a barra superior. Você pode reorganizar a hierarquia e a classificação dos grupos e subgrupos na barra.

![Grupos](media/get-started-portal/groups.png)

Para ocultar ou mostrar colunas nos resultados, selecione **Colunas** acima da tabela e, em seguida, marque ou desmarque as colunas desejadas na lista suspensa.

![Selecionar colunas](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Exibir e modificar gráficos
Você também pode ver os resultados da consulta em formatos visuais. Insira a seguinte consulta como um exemplo:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Por padrão, os resultados aparecem em uma tabela. Selecione **Gráfico** acima da tabela para ver os resultados em uma exibição gráfica.

![Gráfico de barras](media/get-started-portal/bar-chart.png)

Os resultados aparecem em um gráfico de barras empilhadas. Selecione outras opções, como **Coluna Empilhada** ou **Pizza** para mostrar outras exibições dos resultados.

![Gráfico de pizza](media/get-started-portal/pie-chart.png)

Você pode alterar propriedades da exibição, como eixos x e y, ou preferências de agrupamento e divisão, manualmente por meio da barra de controle.

Você também pode definir a visualização preferencial na própria consulta, usando o operador [render](/azure/kusto/query/renderoperator).

## <a name="pin-results-to-a-dashboard"></a>Fixar resultados em um painel

Para fixar uma tabela de resultados ou um gráfico do Log Analytics em um painel compartilhado do Azure, selecione **Fixar no painel** na barra superior. 

![Fixar no painel](media/get-started-portal/pin-dashboard.png)

No painel **Fixar em outro painel**, selecione ou crie um painel compartilhado no qual fixar e selecione **Aplicar**. A tabela ou o gráfico aparece no painel do Azure selecionado.

![Gráfico fixado ao painel](media/get-started-portal/pin-dashboard2.png)

Uma tabela ou um gráfico que você fixa em um painel compartilhado tem as seguintes simplificações: 

- Os dados são limitados aos últimos 14 dias.
- Uma tabela mostra apenas, no máximo, quatro colunas e as sete primeiras linhas.
- Gráficos com muitas categorias discretas agrupam automaticamente as categorias menos populadas em um único compartimento **outras**.

## <a name="save-load-or-export-queries"></a>Salvar, carregar ou exportar consultas

Depois de criar uma consulta, você pode salvar ou compartilhar a consulta ou os resultados com outras pessoas. 

### <a name="save-queries"></a>Salvar consultas

Para salvar uma consulta:

1. Selecione **Salvar** na barra superior.
   
1. Na caixa de diálogo **Salvar**, dê à consulta um **Nome**, usando os caracteres a–z, A–Z, 0–9, espaço, hífen, sublinhado, ponto, parêntese ou barra vertical. 
   
1. Selecione se deseja salvar a consulta como uma **Consulta** ou uma **Função**. Funções são consultas que podem ser referenciadas por outras consultas. 
   
   Para salvar uma consulta como uma função, forneça um **alias de função**, que é um nome curto que outras consultas podem usar para chamar essa consulta.
   
1. Se você estiver em um workspace do Log Analytics, forneça uma **Categoria** para o **Gerenciador de consultas** usar para a consulta. (As categorias não estão disponíveis para consultas do Application Insights)
   
1. Clique em **Salvar**.
   
   ![Salvar função](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Carregar consultas
Para carregar uma consulta salva, selecione **Gerenciador de consultas** no canto superior direito. O painel **Gerenciador de consultas** é aberto, listando todas as consultas por categoria. Expanda as categorias ou insira um nome de consulta na barra de pesquisa e, em seguida, selecione uma consulta para carregá-la no **Editor de consultas**. Você pode marcar uma consulta como **favorita** selecionando a estrela ao lado do nome da consulta.

![Gerenciador de consultas](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Exportar e compartilhar consultas
Para exportar uma consulta, selecione **Exportar** na barra superior e, em seguida, selecione **Exportar para CSV – todas as colunas**, **Exportar para CSV – colunas exibidas** ou **Exportar para o Power BI (consulta M)** na lista suspensa.

O vídeo a seguir mostra como integrar o Log Analytics ao Excel.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Asme]

Para compartilhar um link para uma consulta, selecione **Copiar link** na barra superior e, em seguida, selecione **Copiar link para consulta**, **Copiar texto da consulta** ou **Copiar resultados da consulta** para copiar para a área de transferência. Você pode enviar o link de consulta para outras pessoas que têm acesso ao mesmo workspace.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo tutorial para aprender mais sobre como escrever consultas de log do Azure Monitor.
> [!div class="nextstepaction"]
> [Escrever consultas de log do Azure Monitor](get-started-queries.md)
