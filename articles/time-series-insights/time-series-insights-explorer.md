---
title: Explorar dados usando o explorador - Azure Time Series Insights | Microsoft Docs
description: Aprenda a usar o explorador Azure Time Series Insights para visualizar seus dados de IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b8c2ba54fcc69ba126bf5f68aed99b25f8156155
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046185"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights Explorer

Este artigo descreve os recursos e opções em disponibilidade geral para o [aplicativo web explorador](https://insights.timeseries.azure.com/)azure Time Series Insights . O explorador Time Series Insights demonstra os poderosos recursos de visualização de dados fornecidos pelo serviço e pode ser acessado dentro do seu próprio ambiente.

O Azure Time Series Insights é um serviço totalmente gerenciado de análise, armazenamento e visualização que facilita a exploração e análise de bilhões de eventos de IoT simultaneamente. Ele fornece uma exibição global dos dados, o que permite validar rapidamente sua solução de IoT e evitar um tempo de inatividade dispendioso de dispositivos críticos. Descubra tendências ocultas, detecte anomalias e realize análises de causa raiz quase em tempo real. Atualmente, o Time Series Insights Explorer está em visualização pública.

> [!TIP]
> Para uma visita guiada pelo ambiente de demonstração, leia o [Azure Time Series Insights quickstart](time-series-quickstart.md).

## <a name="video"></a>Vídeo

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Saiba mais sobre a consulta de dados usando o explorador Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Reprodução do vídeo anterior <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Começando com o Time Series Insights usando um Acelerador de Soluções de IoT do Azure".</a>

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar o Time Series Insights Explorer, é necessário:

- Crie um ambiente do Time Series Insights. Para obter mais informações, leia [Como começar com o Time Series Insights](./time-series-insights-get-started.md).
- [Forneça acesso](time-series-insights-data-access.md) à sua conta no ambiente.
- Adicione um [hub de IoT](time-series-insights-how-to-add-an-event-source-iothub.md) ou fonte de eventos do hub de [eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) a ele.

## <a name="explore-and-query-data"></a>Explorar e consultar dados

Alguns minutos após conectar a origem do evento ao ambiente do Time Series Insights, você pode explorar e consultar seus dados de série temporal.

1. Para começar, abra o [explorador Time Series Insights](https://insights.timeseries.azure.com/) no seu navegador. No lado esquerdo da janela, selecione um ambiente. Todos os ambientes aos quais você tem acesso são listados em ordem alfabética.

1. Depois de selecionar um ambiente, use as configurações **De** e **Para** na parte superior ou selecione e arraste sobre o tempo que deseja. Selecione a lupa no canto superior direito ou clique com o botão direito do mouse no intervalo de tempo selecionado e selecione **Pesquisar**.

1. Você também pode atualizar a disponibilidade automaticamente a cada minuto, selecionando o botão **Auto On.** O botão **Auto On** só se aplica ao gráfico de disponibilidade, não ao conteúdo da visualização principal.

1. O ícone da nuvem do Azure leva você ao seu ambiente no portal Azure.

   [![Seleção do ambiente time series insights](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Em seguida, um gráfico que mostra uma contagem de todos os eventos durante o período de tempo selecionado é exibido. Aqui, você tem uma série de controles:

    - **Termos Painel editor**: O espaço de termo é onde você consulta seu ambiente. É encontrado no lado esquerdo da tela:
      - **MEDIDA**: Esta lista de isento mostra todas as colunas numéricas **(Duplas).**
      - **SPLIT BY**: Esta lista de paradas mostra colunas categóricas **(Strings).**
      - Você pode habilitar a interpolação da etapa, mostrar o mínimo e o máximo e ajustar o eixo y do painel de controle próximo a **MEASURE**. Você também pode ajustar se os dados mostrados são uma contagem, média ou soma dos dados.
      - Você pode adicionar até cinco termos para visualizar no mesmo eixo x. Selecione **Adicionar** para adicionar um novo termo ou use o botão **Clone deste termo** para adicionar uma cópia de um termo existente.

        [![Seleção de termos, filtragem e painel de consulta](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predicado**: Use o predicado para filtrar rapidamente seus eventos usando o conjunto de operands listados na tabela a seguir. Se você realizar uma pesquisa selecionando ou clicando, o predicado será atualizado automaticamente com base nessa pesquisa. Os tipos de operando com suporte incluem:

         |Operação  |Tipos com suporte  |Observações  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Dobro,** **hora de data,** **período de tempo**       |         |
         |**=**, **!=**,**<>**     | **String**, **Bool**, **Double,** **DateTime**, **TimeSpan**, **NULL**        |         |
         |**Em**     | **String**, **Bool**, **Double,** **DateTime**, **TimeSpan**, **NULL**        |  Todos os operands devem ser do mesmo tipo ou ser uma constante **NULL.**        |
         |**HAS**     | **Cadeia de caracteres**        |  Apenas literais de cordas constantes são permitidos no lado direito. Corda vazia e **NULL** não são permitidos.       |

      - **Consultas por exemplo**

         [![Exemplo de consultas GA](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Você pode usar a ferramenta de controle deslizante **Tamanho de intervalo** para ampliar e sair de intervalos ao longo do mesmo período de tempo. O controle deslizante fornece um controle mais preciso do movimento entre grandes fatias de tempo que mostram tendências suaves até fatias tão pequenas quanto o milissegundo, que permitem que você exiba e analise cortes granulares e de alta resolução de seus dados. O ponto de partida padrão do controle deslizante é definido como a visão mais ideal dos dados da sua seleção para equilibrar resolução, velocidade de consulta e granularidade.

1. A ferramenta **Escova de tempo** facilita a navegação de um período de tempo para outro.

1. Selecione o ícone **Salvar** para salvar sua consulta atual e compartilhá-la com outros usuários do ambiente. Quando você seleciona o ícone **Abrir,** você pode rever todas as suas consultas salvas e quaisquer consultas compartilhadas de outros usuários em ambientes aos que você tenha acesso.

   [![Consultas](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Visualizar dados

1. Use a ferramenta **Exibição de** perspectiva para uma exibição simultânea de até quatro consultas exclusivas. O botão **Perspectiva Exibir** está no canto superior direito do gráfico.

   [![Selecione consultas para adicionar ao painel de perspectiva](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Exibir um gráfico para explorar visualmente seus dados e usar as ferramentas **Gráfico:**

    - **Selecione** ou **clique** em um tempo específico ou em uma única série de dados.
    - Dentro de uma seleção de tempo, você pode ampliar ou explorar eventos.
    - Em uma série de dados, você pode dividir a série por outra coluna, adicionar a série como um novo termo, mostrar apenas a série selecionada, excluir a série selecionada, executar ping da série ou explorar eventos da série selecionada.
    - Na área de filtro à esquerda do gráfico, você pode rever todas as séries de dados exibidas e reordenar por valor ou nome. Você também pode visualizar todas as séries de dados ou qualquer série fixada ou não fixada. Você pode selecionar uma única série de dados e dividir a série por outra coluna, adicionar a série como um novo termo, mostrar apenas a série selecionada, excluir a série selecionada, fixar essa série ou explorar eventos da série selecionada.
    - Quando você visualiza vários termos simultaneamente, você pode empilhar, desempilhar, revisar dados adicionais sobre uma série de dados e usar o mesmo eixo y em todos os termos. Use os botões no canto superior direito do gráfico.

    [![Configurações de opção de canto superior direito da ferramenta de gráfico](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Use o **mapa de calor** para detectar rapidamente séries de dados únicas ou anômalas em uma determinada consulta. Apenas um termo de pesquisa pode ser visualizado como um mapa de calor.

    [![Gráfico de mapa de calor do explorador GA](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Quando você explora eventos selecionando ou clicando com o botão direito do mouse, o painel **EVENTOS** é disponibilizado. Aqui, você pode rever todos os seus eventos brutos e exportar seus eventos como arquivos JSON ou CSV. Time Series Insights armazena todos os dados brutos.

    [![Eventos](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Selecione a guia **STATS** depois de explorar eventos para expor padrões e estatísticas de colunas.

    - **Padrões**: Este recurso superfície proativamente os padrões mais estatisticamente significativos em uma região de dados selecionada. Você não precisa olhar para milhares de eventos para entender quais padrões requerem mais tempo e energia. Com o Time Series Insights, você pode saltar diretamente para esses padrões estatisticamente significativos para continuar conduzindo uma análise. Esse recurso também é útil para investigações post-mortem em dados históricos.
    - **Estatísticas da coluna**: As estatísticas da coluna fornecem gráficos e tabelas que dividem dados de cada coluna da série de dados selecionada no intervalo de tempos selecionado.

      [![Gráficos e opções de colunas STATS](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Agora você aprendeu sobre os principais recursos, configurações e opções de exibição que estão disponíveis no aplicativo web explorador de datas de tempo.

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [diagnosticar e resolver problemas](time-series-insights-diagnose-and-solve-problems.md) em seu ambiente Time Series Insights.

- Faça o tour guiado [da Série Do Tempo Azure Insights.](time-series-quickstart.md)
