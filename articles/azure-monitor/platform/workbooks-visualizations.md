---
title: Visualizações da agenda de trabalho do Azure Monitor
description: Conheça todos os componentes de visualização da pasta de trabalho do Azure Monitor, incluindo - texto, gráficos, grades, árvores e gráficos.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658023"
---
# <a name="azure-monitor-workbook-visualizations"></a>Visualizações da agenda de trabalho do Azure Monitor

As atos de trabalho do Azure Monitor suportam vários estilos diferentes de visualizações para atender às suas necessidades de relatórios. Este artigo fornece exemplos de cada tipo de visualização.

## <a name="text"></a>Texto

Os livros de trabalho permitem que os autores incluam blocos de texto em suas livros de trabalho. O texto pode ser análise humana de telemetria, informações para ajudar os usuários a interpretar seus dados, títulos de seção, etc.

![Captura de tela da tabela de texto da Apdex](./media/workbooks-visualizations/apdex.png)

O texto é adicionado através de um controle Markdown que fornece controle de formatação completa.

![Captura de tela de marcação bruta que constrói a tabela renderizada](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Adicione um controle de texto

1. Alterne a carteira de trabalho para editar o modo clicando no item **Editar** barra de ferramentas.
2. Use o **link Adicionar texto** para adicionar um controle de texto à carteira de trabalho.
3. Adicione Markdown ao controle.
4. Clique no botão **Edição Feita** para ver o texto formatado.

> [!TIP]
> Use esta [planilha de trapaça do Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) para aprender sobre diferentes opções de formatação.

## <a name="charts"></a>Gráficos

As carteiras de trabalho permitem que os dados de monitoramento sejam apresentados como gráficos. Os tipos de gráficos suportados incluem linha, barra, barra categórica, área, parcelas de dispersão, torta e tempo. Os autores podem optar por personalizar a altura, largura, paleta de cores, legenda, títulos, mensagem sem dados, etc. do gráfico.

Os gráficos de suporte de livros de trabalho para registros e fontes métricas de dados. 

### <a name="adding-a-log-chart"></a>Adicionando um gráfico de log

1. Alterne a carteira de trabalho para editar o modo clicando no item **Editar** barra de ferramentas.
2. Use o **link Adicionar consulta** para adicionar um controle de consulta de log à carteira de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Crições de aplicativo) e os recursos a serem direcionados.
4. Use o editor de consulta para inserir o [KQL](https://docs.microsoft.com/azure/kusto/query/) para sua análise (por exemplo, tendência de solicitações).
5. Defina a visualização para uma das: **Área,** **Barra,** **Barra (categórica)**, **Linha,** **Torta,** **Dispersão**ou **Tempo**.
6. Defina outros parâmetros, se necessário - como intervalo de tempo, visualização, tamanho, paleta de cores e legenda.

![Captura de tela do gráfico de log no modo de edição](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parâmetros do gráfico de log

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Query Type` | O tipo de consulta a ser usada | Log, Gráfico de Recursos do Azure, etc. |
| `Resource Type` | O tipo de recurso para segmentar | Insights de aplicativos, análises de log ou azure-first |
| `Resources` | Um conjunto de recursos para obter o valor das métricas | MyApp1 |
| `Time Range` | A janela de tempo para visualizar o gráfico de registro | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização para usar | Área, Bar, Linha, Torta, Dispersão, Tempo, barra categórica |
| `Size` | O tamanho vertical do controle | Pequeno, médio, grande ou cheio |
| `Color palette` | A paleta de cores a ser usada no gráfico. Ignorado no modo multimétrico ou segmentado. | Azul, verde, vermelho, etc. |
| `Legend` | A função de agregação para usar para a legenda | Soma ou Média de valores ou Max, Min, Primeiro, Último valor |
| `Query` | Qualquer consulta KQL que retorne dados no formato esperado pela visualização do gráfico | _solicitações \| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h_ |

### <a name="adding-a-metric-chart"></a>Adicionando um gráfico métrico

1. Alterne a carteira de trabalho para editar o modo clicando no item **Editar** barra de ferramentas.
2. Use o link **Adicionar métrica** para adicionar um controle métrico à carteira de trabalho.
3. Selecione um tipo de recurso (por exemplo, Conta de armazenamento), os recursos a serem direcionados, o namespace e o nome métricos e a agregação a ser usada.
4. Defina outros parâmetros, se necessário - como intervalo de tempo, bipe, visualização, tamanho e paleta de cores.

![Captura de tela do gráfico métrico no modo de edição](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parâmetros do gráfico métrico

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Resource Type` | O tipo de recurso para segmentar | Armazenamento ou Máquina Virtual. |
| `Resources` | Um conjunto de recursos para obter o valor das métricas | MyStorage1 |
| `Namespace` | O namespace com a métrica | Blob de > de armazenamento |
| `Metric` | A métrica para visualizar | Armazenamento > transações > Blob |
| `Aggregation` | A função de agregação para aplicar à métrica | Soma, Contagem, Média, etc. |
| `Time Range` | A janela de tempo para visualizar a métrica em | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização para usar | Área, Barra, Linha, Dispersão, Grade |
| `Split By` | Opcionalmente, divida a métrica em uma dimensão | Transações por tipo Geo |
| `Size` | O tamanho vertical do controle | Pequeno, médio ou grande |
| `Color palette` | A paleta de cores a ser usada no gráfico. Ignorado se `Split by` o parâmetro é usado | Azul, verde, vermelho, etc. |

## <a name="grids"></a>Grades

Grades ou tabelas são uma maneira comum de apresentar dados aos usuários. Os workbooks permitem que os usuários estilizem individualmente as colunas da grade para fornecer uma ui rica para seus relatórios.

O exemplo abaixo mostra uma grade que combina ícones, mapas de calor e barras de faísca para apresentar informações complexas. A carteira de trabalho também fornece classificação, uma caixa de pesquisa e um botão de análise.

![Captura de tela da grade baseada em log](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Adicionando uma grade baseada em log

1. Alterne a carteira de trabalho para editar o modo clicando no item **Editar** barra de ferramentas.
2. Use o **link Adicionar consulta** para adicionar um controle de consulta de log à carteira de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Crições de aplicativo) e os recursos a serem direcionados.
4. Use o editor de consulta para inserir o KQL para sua análise (por exemplo, VMs com memória abaixo de um limite)
5. Defina a visualização para **Grade**
6. Defina outros parâmetros, se necessário - como intervalo de tempo, tamanho, paleta de cores e legenda.

![Captura de tela da consulta de grade baseada em log](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Blocos

Os azulejos são uma maneira muito útil de apresentar dados resumidos em livros de trabalho. A imagem abaixo mostra um caso de uso comum de telhas - resumo do nível do aplicativo em cima de uma grade detalhada.

![Captura de tela da exibição do resumo dos azulejos](./media/workbooks-visualizations/tiles-summary.png)

Os ladrilhos da área de trabalho suportam a exibição de um título, legenda, texto grande, ícones, gradientes baseados em métricas, linha de faísca/barras, rodapé, etc.

### <a name="adding-a-tile"></a>Adicionando um azulejo

1. Alterne a carteira de trabalho para editar o modo clicando no item _Editar_ barra de ferramentas.
2. Use o **link Adicionar consulta** para adicionar um controle de consulta de log à carteira de trabalho. 
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Crições de aplicativo) e os recursos a serem direcionados.
4. Use o editor de consulta para inserir o KQL para sua análise
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Definir tamanho para **completo**
6. Defina a visualização para **Telhas**
7. Clique no botão **Configurações do azulejo** para abrir o painel de configurações
8. Em **campos de azulejos,** conjunto:
    * Título: `name`
    * Esquerda: `Requests`, Renderizador: `Big Number`, `Green to Red`Paleta de cores: , Valor de min:`0`
    * Fundo:`appName`
9. Clique no botão **Salvar e Fechar** na parte inferior do painel.

![Captura de tela da exibição do resumo dos azulejos](./media/workbooks-visualizations/tile-settings.png)

É assim que as telhas ficarão no modo de leitura:

![Captura de tela da exibição do resumo dos azulejos](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Árvores

Os livros de trabalho suportam visualizações hierárquicas através de grades de árvores. As árvores permitem que algumas linhas sejam expansíveis para o próximo nível para uma experiência de perfuração.

O exemplo abaixo mostra as métricas de saúde do contêiner (tamanho do conjunto de trabalho) visualizadas como uma grade de árvores. Os nós de nível superior aqui são nós do Azure Kubernetes Service (AKS), o próximo nível são pods e o nível final são contêineres. Observe que você ainda pode formatar suas colunas como em uma grade (mapa de calor, ícones, link). A fonte de dados subjacente neste caso é um espaço de trabalho do Log Analytics com logs AKS.

![Captura de tela da exibição do resumo dos azulejos](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Adicionando uma grade de árvore
1. Alterne a carteira de trabalho para editar o modo clicando no item _Editar_ barra de ferramentas.
2. Use o **link Adicionar consulta** para adicionar um controle de consulta de log à carteira de trabalho. 
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Crições de aplicativo) e os recursos a serem direcionados.
4. Use o editor de consulta para inserir o KQL para sua análise
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Defina a visualização para **Grade**
6. Clique no botão **Configurações da coluna** para abrir o painel de configurações
7. Na seção **Árvore/Grupo Por Configurações** na parte inferior, definido:
    * Tipo de árvore:`Parent/Child`
    * Id Field:`Id`
    * Campo de id dos pais:`ParentId`
    * Mostre o expansor em:`Name`
    * Expanda o nível superior da árvore:`checked`
8. Na seção _Colunas_ na parte superior, definir:
    * _Id_ - Renderizador de Coluna:`Hidden`
    * _ID pai_ - Renderizador de coluna:`Hidden`
    * _Pedidos_ - Renderizador `Bar`de `Blue`coluna: , Cor: , Valor mínimo:`0`
9. Clique no botão _Salvar e Fechar_ na parte inferior do painel.    

![Captura de tela da exibição do resumo dos azulejos](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Configurações da árvore

| Configuração | Explicação |
|:------------- |:-------------|
| `Id Field` | A identificação única de cada linha na grade |
| `Parent Id Field` | A id do pai da linha atual |
| `Show the expander on` | A coluna em que mostrar o expansor de árvores. É comum que as redes de árvores escondam seu campo de id e de id dos pais porque não são muito legíveis. Em vez disso, o expansor aparece em um campo com um valor mais legível - como o nome da entidade |
| `Expand the top level of the tree` | Se verificada, a rede de árvores será expandida no nível superior. Útil se você quiser mostrar mais informações por padrão |

## <a name="graphs"></a>Gráficos

As carteiras de trabalho suportam a visualização de gráficos arbitrários com base em dados de registros para mostrar as relações entre as entidades de monitoramento.

O gráfico abaixo mostra dados que fluem para dentro/para fora de um computador através de várias portas para/de computadores externos. Ele é colorido por tipo (computador vs. porta vs. IP externo) e os tamanhos de borda correspondem à quantidade de dados que fluem no meio. Os dados subjacentes vêm da consulta KQL que visa conexões VM.

![Captura de tela da exibição do resumo dos azulejos](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Adicionando um gráfico
1. Alterne a carteira de trabalho para editar o modo clicando no item _Editar_ barra de ferramentas.
2. Use o **link Adicionar consulta** para adicionar um controle de consulta de log à carteira de trabalho. 
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Crições de aplicativo) e os recursos a serem direcionados.
4. Use o editor de consulta para inserir o KQL para sua análise
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. Defina a visualização para **Gráfico**
8. Clique no botão **Configurações do gráfico** para abrir o painel de configurações
9. Em _Campos de layout_ na parte inferior, definido:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. Em _Configurações de formato de nó_ na parte superior, definido:
    * _Conteúdo superior_- `Name`Usar coluna: , Renderizador de coluna:`Text`
    * _Conteúdo central_- `Calls`Usar coluna: `Big Number`, Renderizador de coluna: , Paleta de cores:`None`
    * _Conteúdo inferior_- `Kind`Usar coluna: , Renderizador de coluna:`Text`
10. Clique no botão _Salvar e Fechar_ na parte inferior do painel.

![Captura de tela da exibição do resumo dos azulejos](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Próximas etapas

* [Implantar](workbooks-automate.md) livros de trabalho com o Azure Resource Manager.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da sua carteira de trabalho.
