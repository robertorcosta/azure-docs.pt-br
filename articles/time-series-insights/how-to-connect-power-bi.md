---
title: Conecte seu ambiente ao Power BI - Azure Time Series Insights | Microsoft Docs
description: Saiba como conectar o Azure Time Series Insights ao Power BI para compartilhar, mapear e exibir dados em toda a sua organização.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863835"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Visualize dados de Time Series Insights no Power BI

O Azure Time Series Insights é uma plataforma para armazenar, gerenciar, consultar e visualizar dados de séries tempontos na nuvem. [Power BI](https://powerbi.microsoft.com) é uma ferramenta de análise de negócios com recursos de visualização ricos que permite compartilhar insights e resultados em toda a sua organização. Ambos os serviços agora podem ser integrados para obter o melhor dos recursos de visualização inerentes ao Time Series Insights, bem como aos Power BI's.

Você aprenderá a:

* Conecte insights da série de tempo ao Power BI usando o conector de nuvem
* Crie visuais com seus dados no Power BI
* Publique o relatório para power bi e compartilhe com o resto da sua organização

No final, você aprenderá a visualizar dados de séries tempois através do Azure Time Series Insights e melhorá-los com a forte visualização de dados e recursos de fácil compartilhamento do Power BI.

Certifique-se de se inscrever para uma [assinatura gratuita do Azure](https://azure.microsoft.com/free/) se você ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* Baixe e instale a versão mais recente do [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Ter ou criar [uma instância de visualização de visualizações da série do tempo do Azure](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> O conector Power BI é atualmente suportado em ambientes *pay-as-you-go* de visualização de séries temporias configurados para **warm store**.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Conecte dados de Insights de séries tempoatos ao Power BI

Para conectar seu ambiente de Insights da série do tempo ao Power BI, siga estas etapas:

1. Open Time Series Insights Explorer
1. Exportar dados como consulta ou como dados brutos
1. Área de trabalho Open Power BI
1. Carregar da consulta personalizada

### <a name="export-data-into-power-bi-desktop"></a>Exportar dados para desktop Power BI

Introdução:

1. Abra o Explorador de Visualização de Insights da Série Do Tempo e faça a curadoria de seus dados.
1. Depois de criar uma exibição com a que você está satisfeito, navegue até o menu suspenso **Mais ações** e selecione Conectar ao **POWER BI**.

    [![Exportação do Explorador de visualização de visualização de séries tempoires](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Defina seus parâmetros dentro desta guia:

   1. Especifique um prazo relativo para visualização. Se você está feliz com sua visão existente, deixe isso como **prazo existente**.
   
   1. Escolha entre **Eventos Agregados** e **Brutos**. 
   
       > [!NOTE]
       > Você sempre pode agregar seus dados mais tarde no Power BI, mas não pode reverter para dados brutos após a agregação. 
       
       > [!NOTE]
       > Há um limite de contagem de eventos de 100 K para dados de nível de Evento Bruto.

       [![Conectar](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Se você não tiver configurado a instância de Insights da série do tempo para **warm store,** você receberá um aviso.

       [![Aviso de loja quente](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Você pode configurar sua instância existente para **Warm Store** no portal Azure.

1. Selecione **Copiar consulta à área de transferência**.
1. Agora, inicie o Power BI Desktop.
1. Na área de trabalho do Power BI na guia **Home,** **selecione Obter dados** no canto superior esquerdo e, em **seguida, Mais**.

    [![Dropdown residencial](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Procure **insights da série do tempo,** selecione **Azure Time Series Insights (Beta)** e **conecte.**

    [![Conecte o POWER BI aos insights da série do tempo](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Alternativamente, navegue até a guia **Azure,** selecione **Azure Time Series Insights (Beta)** e **conecte.**
    
1. Uma caixa de diálogo de mensagem será exibida pedindo permissão para se conectar a recursos de terceiros. Selecione **continuar**.

    [![Escolha Criar consulta personalizada](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. No menu suspenso em **Data Source,** escolha **Criar consulta personalizada**. Cole da área de transferência no campo **opcional de consulta personalizada (opcional)** abaixo e pressione **OK**.

    [![Passe na consulta personalizada e selecione OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. A tabela de dados será agora carregada. Pressione **Carregar** para carregar em Power BI.

    [![Revise os dados carregados na tabela e selecione Carregar](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Se você tiver concluído essas etapas, pule para a próxima seção.

## <a name="create-a-report-with-visuals"></a>Criar um relatório com elementos visuais

Agora que você importou os dados para o Power BI, é hora de construir um relatório com visuais.

1. No lado esquerdo da janela, certifique-se de ter selecionado a exibição **'Relatório'.**

    [![Selecione a exibição de relatórios](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Na coluna **Visualizações,** selecione seu visual de escolha. Por exemplo, selecione **Gráfico de linha**. Isso adicionará um gráfico de linha em branco à sua tela.
 
1.  Na lista **Campos,** selecione **Carimbo de tempo** e arraste-o para o campo **Axis** para exibir itens ao longo do eixo X.

1.  Novamente, na lista **Campos,** selecione **TimeSeriesId** e arraste-o para o campo **Valores** para exibir itens ao longo do eixo Y.

    [![Criar um gráfico de linhas](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Para adicionar outro gráfico à sua tela, selecione em qualquer lugar da tela fora do gráfico de linha e repita este processo.

    [![Crie gráficos adicionais para compartilhar](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Depois de criar seu relatório, você pode publicá-lo no Power BI Reporting Services.

## <a name="advanced-editing"></a>Edição avançada

Se você já carregou um conjunto de dados no Power BI, mas gostaria de modificar a consulta (como os parâmetros de data/hora ou ID do ambiente), você pode fazê-lo através da funcionalidade Advanced Editor do Power BI. Consulte a [documentação](https://docs.microsoft.com/power-bi/desktop-query-overview) do Power BI para saber mais.

Como uma visão geral:

1. Na área de trabalho do Power BI, selecione **Editar consultas**.
1. **Imprensa Editor Avançado**.

    [![Editar consultas no Editor Avançado](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Modifique a carga útil JSON conforme desejado.
1. Selecione **'Feito'** e, em seguida, **feche & Aplicar** dentro da janela do editor de consulta de energia **.**

A interface agora refletirá as alterações desejadas que você aplicou.  

## <a name="next-steps"></a>Próximas etapas

* Leia sobre [os conceitos de conector power bi](https://docs.microsoft.com/power-bi/desktop-query-overview) para azure Time Series Insights.

* Saiba mais sobre [o desktop Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Leia o Explorador de [visualização](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) do Ga Explorer e [do Time Series Insights .](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)
