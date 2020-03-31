---
title: Visualize dados no explorador de visualização - Azure Time Series Insights | Microsoft Docs
description: Conheça os recursos e opções disponíveis no explorador de visualização de insights da série do tempo do Azure.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: c117510749abcf997e414371faa6dea1dc79ea7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75861754"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Explorador de visualização da série do tempo do Azure

Este artigo descreve os vários recursos e opções disponíveis no [aplicativo web dedemonstração](https://insights.timeseries.azure.com/preview/demo)do Azure Time Series Insights Preview .

## <a name="prerequisites"></a>Pré-requisitos

Para começar com o explorador de visualização de visualizações da série do tempo do Azure, você deve:

* Tenha um ambiente de Insights de série sinuosa provisionado. Saiba mais sobre o provisionamento de uma instância lendo o tutorial de visualização da série do tempo do [Azure.](./time-series-insights-update-create-environment.md)
* [Forneça acesso](./time-series-insights-data-access.md) aos dados do ambiente Time Series Insights que você criou para a conta. Você pode fornecer acesso a outras pessoas, bem como para si mesmo.
* Adicione uma fonte de evento ao ambiente Time Series Insights para empurrar dados para o ambiente:
  * [Saiba como se conectar a um hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * Aprenda [a se conectar a um hub de IoT](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Explorar o explorador de visualização de insights da série do tempo

O explorador de visualização da série do tempo do Azure consiste nos sete elementos a seguir:

[![Visão geral do explorador de visualização de visualização de séries tempois](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Painel ambiente](#1-environment-panel): Exibe todos os ambientes do Azure Time Series Insights.
1. [Barra de navegação](#2-navigation-bar): Permite alternar entre as **páginas Analisar** e **Model.**
1. [Árvore de hierarquia e painel de pesquisa](#3-hierarchy-tree-and-search-panel): Permite selecionar e procurar elementos de dados específicos a serem mapeados.
1. [Série sotempo bem](#4-time-series-well): Mostra todos os seus elementos de dados selecionados no momento.
1. [Painel gráfico](#5-chart-panel): Exibe o gráfico de trabalho atual.
1. [Linha do tempo](#6-time-editor-panel): Permite modificar seu tempo de trabalho.
1. [Barra de aplicativos](#7-app-bar): Contém suas opções de gerenciamento de usuário (como o inquilino atual) e permite que você as altere e as configurações do idioma.


## <a name="1-environment-panel"></a>1. Painel de meio ambiente

O painel de ambiente exibe todos os ambientes do Time Series Insights, que você tem acesso. A lista inclui ambientes pay-as-you-go (Preview), bem como ambientes S1/S2 (Disponibilidade Geral). Basta selecionar o ambiente Time Series Insights que você deseja usar para ser imediatamente levado para lá.

1. Selecione a seta suspensa ao lado do ambiente exibido.

   [![Painel de ambiente](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Em seguida, selecione o ambiente que deseja.

## <a name="2-navigation-bar"></a>2. Barra de navegação

  [![A barra de navegação](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Use a barra de navegação para selecionar entre dois pontos de exibição:

* **Analise**: Use-o para mapear e executar análises ricas em seus dados de séries tempométricas modeladas ou não modeladas.
* **Modelo**: Use-o para empurrar novos tipos, hierarquias e instâncias de visualização de séries tempois para o modelo de Insights da série do tempo.

### <a name="model-authoring"></a>Autoria de modelo

O Azure Time Series Insights Preview suporta operações completas de criação, leitura, atualização e exclusão (CRUD) em seu Modelo de Série soda.

[![O painel de pesquisa do modelo](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Tipo de modelo de série de tempo**: Você pode usar os tipos Time Series Insights para definir variáveis ou fórmulas para fazer cálculos. Eles estão associados a uma dada instância de Insights de séries tempo. Um tipo pode ter uma ou mais variáveis.
* **Hierarquia do modelo de séries**tempo : Hierarquias são organizações sistemáticas de seus dados. Hierarquias descrevem as relações entre entidades diferentes em seus dados de Azure Time Series Insights.
* **Instância do modelo da série temporal**: As instâncias são as próprias séries tempo. Na maioria dos casos, eles são o **DeviceID** ou **AssetID**, que é o identificador único do ativo no ambiente.

Para saber mais sobre o Modelo de Série sinuosa, leia [Modelos da Série Times](./time-series-insights-update-tsm.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. Árvore de hierarquia e painel de pesquisa

A árvore de hierarquia e o painel de pesquisa permitem que você pesquise e navegue facilmente na hierarquia [do Modelo de Séries](./time-series-insights-update-tsm.md) tempontos para encontrar as instâncias específicas de séries de tempo que você deseja exibir em seu gráfico. Quando você seleciona suas instâncias, elas não só são adicionadas ao gráfico atual, mas também são adicionadas bem aos dados. 

[![Árvore de hierarquia e painel de pesquisa](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

O painel de resultados da pesquisa também permite que você visualize seus resultados em uma exibição de hierarquia ou uma exibição de lista, facilitando a visualização das instâncias que deseja exibir.
 
## <a name="4-time-series-well"></a>4. Série soerada bem

O poço exibe campos de instância e outros metadados associados às instâncias selecionadas do Time Series Insights. Ao selecionar as caixas de seleção no lado direito, você pode ocultar ou exibir instâncias específicas do gráfico atual. 

  [![O poço de visualização](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Você pode remover bem elementos de dados específicos de seus dados atuais selecionando o controle **de Excluir** (lata de lixo) vermelho no lado esquerdo do elemento. O poço também permite controlar como cada elemento é exibido no gráfico. Você pode optar por adicionar sombras min/max, pontos de dados, mudar o elemento no tempo e visualizar a instância de forma escalonada. 

Além disso, o controle de explorações permite criar mudanças de tempo e dispersar tramas facilmente.  

  [![Opções de layout de poços](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Se a mensagem a seguir for exibida, a ocorrência não terá nenhum dado durante o período de tempo selecionado. Para resolver o problema, aumente o período de tempo ou confirme se a instância está empurrando dados.
>
> ![Nenhuma notificação de dados](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. Painel gráfico

O gráfico permite que você exiba instâncias de séries tempoéres como linhas. Você pode recolher o painel de ambiente, o modelo de dados e o painel de controle de intervalo de tempo, clicando em controles da web para tornar o gráfico maior. 

  [![Visão geral do gráfico de visualização](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Tipo de gráfico**: Controles quais elementos de dados estão disponíveis para visualização.

1. **Tamanho do intervalo**: A ferramenta de controle deslizante de tamanho de intervalo permite que você amplie e saia de intervalos ao longo do mesmo período de tempo. Isso fornece um controle mais preciso do movimento entre grandes fatias de tempo que mostram tendências suaves até fatias tão pequenas quanto o milissegundo, permitindo que você revise cortes granulares e de alta resolução de seus dados. O ponto de partida padrão do controle deslizante é definido como a exibição mais ideal dos dados na seleção: balanceamento de resolução, velocidade de consulta e granularidade.

1. **Zoom e panela**: Selecione este controle para ampliar e panar o gráfico.

1. **Controle do eixo Y:** Ciclos através das opções de exibição do eixo y disponíveis:

    * `Stacked`: Cada linha tem um eixo Y individual.
    * `Overlap`: Use-o para empilhar várias linhas no mesmo eixo Y, com a alteração de dados do eixo Y com base na linha selecionada.
    * `Shared`: Todos os dados do eixo Y exibidos juntos.

1. **Elemento marcador**: O elemento de dados atualmente selecionado e seus detalhes associados.

Você pode perfurar ainda mais uma fatia de dados específica **clicando** no ponto de dados no gráfico atual enquanto segura o mouse e, em seguida, arrastando a área selecionada para o ponto final de sua escolha. **Clique com** o botão direito do mouse na área azul selecionada e selecione **Zoom** como mostrado abaixo. Você também pode visualizar e baixar os eventos de telemetria no timespan selecionado.

  [![Zoom do gráfico de visualização](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Depois de executar a ação **Zoom,** o conjunto de dados selecionado será exibido. Selecione o controle de formato para percorrer as três representações do eixo Y dos dados do Time Series Insights.

  [![Pré-visualização gráfico y-eixo](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Aqui, um exemplo de um **gráfico sobreposto** é fornecido:

  [![Opção de gráfico sobreposto](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

O botão **Mais ações** se expande para exibir o Download **como CSV,** **Conectar-se ao Power BI,** **mostrar dados de gráficos como uma tabela**e explorar opções de eventos **brutos.**

  [![Mais opções de ações](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Saiba mais sobre a opção **Connect to Power BI** no [conector nativo power BI do Time Series Insights](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. Painel do editor de tempo

Ao trabalhar com o Time Series Insights, primeiro você selecionará um período de tempo. O período de tempo selecionado controlará o conjunto de dados disponível para manipulação com os widgets de atualização do Time Series Insights.

  [![Painel de seleção de tempo](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Uma parte da linha do tempo é destacada em uma cor âmbar ou laranja para indicar a extensão dos dados que estão disponíveis no warm store.

Os seguintes controles da Web estão disponíveis na atualização time series insights para selecionar seu tempo de trabalho. 

  [![Controle de poços de exploração](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Controle deslizante de intervalo de data interno**: Use os dois controles de ponto final arrastando-os ao longo do período de tempo que você deseja. Este intervalo de data sumido interno é limitado pelo controle de controle deslizante de faixa de data externa.

1. **Aumentar e diminuir os botões de intervalo de data**: Aumente ou diminua o tempo selecionando qualquer botão para o intervalo desejado.

1. **Controle de colapso do intervalo**de tempo : Este controle da web permite que você esconda todos os controles, exceto a ferramenta de controle deslizante de intervalo de data interna.

1. **Controle deslizante de faixa de data externa :** Use os controles de ponto final para selecionar o intervalo de datas externas, que estará disponível para o controle interno do intervalo de datas.

1. **Controle de controle deslizante de intervalo de tempo**: Use-o para alternar rapidamente entre seleções de intervalo de tempo predefinidas, como os últimos **30 minutos,** as **últimas 12 horas**ou um intervalo **personalizado**. A alteração desse valor também muda os intervalos de intervalo disponíveis discutidos na ferramenta de controle deslizante de tamanho do intervalo.

   [![De e para o painel de seleção](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. Barra de aplicativo

O painel de navegação Time Series Insights Preview aparece na parte superior do aplicativo Time Series Insights. Ele fornece as seguintes funcionalidades:

### <a name="current-session-share-link-control"></a>Controle de link de compartilhamento de sessão atual

  [![Ícone de compartilhamento](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Selecione o novo ícone **Compartilhar** para compartilhar um link de URL com sua equipe.

  [![Compartilhe sua URL de exemplo](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Seção de Locatário

  [![Seleção de inquilinos](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Exibe as informações atuais da conta de login do Time Series Insights.
* Use-o para alternar entre os temas disponíveis do Time Series Insights.
* Use-o para exibir o [aplicativo web de demonstração de](https://insights.timeseries.azure.com/preview/demo)visualização .

### <a name="theme-selection"></a>Seleção de tema

Para selecionar um novo tema, selecione o ícone de perfil localizado no canto superior direito. Em seguida, selecione **Alterar tema**.

  [![Seleção de tema](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> A seleção de idiomas também está disponível selecionando o ícone do seu perfil.

O Azure Time Series Insights Preview suporta dois temas:

* **Tema de luz**: O tema padrão mostrado ao longo deste documento.
* **Tema escuro :** Renderiza o explorador como mostrado aqui:

  [![Tema escuro selecionado](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>Controles de ambiente S1/S2

### <a name="preview-terms-panel"></a>Painel de termos de visualização

Esta seção se aplica somente a ambientes existentes do S1/S2 que tentam usar o gerenciador na interface do usuário atualizada. Você pode querer usar o produto geralmente disponível e visualizar em combinação. Adicionamos algumas funcionalidades de interface do usuário existente para ao gerenciador atualizado, mas você pode obter a experiência de interface do usuário completa para o ambiente S1/S2 no gerenciador do Time Series Insights existente. 

Em vez da hierarquia, o painel de termos Do time series insights é exibido. O painel de termos permite definir consultas em seu ambiente. Use-os também para filtrar dados com base em um predicado.

  [![Onde painel de consulta](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

O painel de editor de termos de visualização do Azure Time Series Insights leva em consideração os seguintes parâmetros:

**Onde**: Use a cláusula onde filtrar rapidamente seus eventos usando o conjunto de operações listados na tabela a seguir. Caso você realize uma pesquisa selecionando um operando, o predicado é atualizado automaticamente com base nessa pesquisa. Os tipos de operações suportadas incluem o seguinte:

| Operação | Tipos com suporte   | Observações |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Todos os operandos devem ser do mesmo tipo ou ser uma constante NULL. |
| `HAS` | String | Apenas literais de cordas constantes são permitidos no lado direito. Corda vazia e NULL não são permitidos. |

Para saber mais sobre operações de consulta suportadas e tipos de dados, leia [TSX (Time Series Expression, expressão em série de tempo)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

### <a name="examples-of-where-clauses"></a>Exemplos de onde cláusulas

  [![Onde exemplos de cláusulas](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Medida**: Uma lista baixa que exibe todas as colunas numéricas **(Doubles)** que você pode usar como elementos para o gráfico atual.

**Dividido por**: Esta lista de baixa exibe todas as colunas categóricas disponíveis (Strings) em seu modelo que você pode agrupar seus dados. Você pode adicionar até cinco termos para visualizar no mesmo eixo x. Digite os parâmetros desejados e selecione **Adicionar** para adicionar um novo termo.

  [![Vista consultada e filtrada um](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Você pode mostrar e ocultar elementos no painel de gráficos selecionando o ícone visível, como mostrado na imagem a seguir. Para remover completamente as consultas, selecione o **vermelho X**.

  [![Cancelar uma opção consultada e filtrada](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [armazenamento e inserção](./time-series-insights-update-storage-ingress.md) na visualização de insights da série do tempo do Azure.

- Leia o documento de visualização de insights da série do tempo sobre [modelagem de dados](./time-series-insights-update-tsm.md).

- Aprenda [a diagnosticar e solucionar problemas na](./time-series-insights-update-how-to-troubleshoot.md) instância do Time Series Insights.
