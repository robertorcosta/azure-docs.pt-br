---
title: Conecte seu ambiente ao Power BI-Azure Time Series Insights | Microsoft Docs
description: Saiba como conectar Azure Time Series Insights a Power BI para compartilhar, exibir gráficos e mostrar dados em sua organização.
author: deepakpalled
ms.author: dpalled
manager: diviso
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 6825627b77e3ea227cad88e347dbbabd5e6f5d0c
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540329"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Visualizar dados de Azure Time Series Insights no Power BI

Azure Time Series Insights é uma plataforma para armazenar, gerenciar, consultar e Visualizar dados de série temporal na nuvem. [Power bi](https://powerbi.microsoft.com) é uma ferramenta de análise de negócios com recursos de visualização avançados que permite que você compartilhe informações e resultados em toda a sua organização. Ambos os serviços agora podem ser integrados para obter o melhor dos dois Azure Time Series Insights recursos de visualização inerentes, bem como Power BI.

Você aprenderá a:

* Conectar Azure Time Series Insights ao Power BI usando o conector de nuvem
* Criar visuais com seus dados no Power BI
* Publicar o relatório para Power BI e compartilhá-lo com o restante da sua organização

Ao final, você aprenderá como Visualizar dados de série temporal por meio de Azure Time Series Insights e aprimorá-los com a visualização de dados forte e recursos de compartilhamento fácil de Power BI.

Lembre-se de inscrever-se para uma [assinatura gratuita do Azure](https://azure.microsoft.com/free/) se você ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* Baixe e instale a versão mais recente do [Power bi desktop](https://powerbi.microsoft.com/downloads/)
* Ter ou criar um [ambiente de Gen2 de Azure Time Series insights](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
>
> * Há suporte para o conector atualmente em ambientes Azure Time Series Insights Gen2 configurados com **somente armazenamento quente**.
> * Se você tiver acesso de convidado ao ambiente de Azure Time Series Insights Gen2 de outro locatário do Azure AD, você não poderá acessar o conector. Leia sobre as [políticas de acesso do ambiente](./concepts-access-policies.md).

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>Conectar dados de Azure Time Series Insights para Power BI

Para conectar seu ambiente de Azure Time Series Insights ao Power BI, siga estas etapas:

1. Abrir Azure Time Series Insights Explorer
1. Exportar dados como uma consulta ou dados brutos
1. Abrir o Power BI Desktop
1. Carregar da consulta personalizada

### <a name="export-data-into-power-bi-desktop"></a>Exportar dados para o Power BI desktop

Introdução:

1. Abra o Azure Time Series Insights Explorer e organizar seus dados.
1. Depois de criar uma exibição com a qual você está satisfeito, navegue até o menu suspenso **mais ações** e selecione **conectar-se a Power bi**.

    [![Exportação do Azure Time Series Insights Explorer](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Defina os parâmetros nesta guia:

   1. Especifique um período de tempo relativo a ser exibido. Se você estiver satisfeito com o modo de exibição existente, deixe isso como um **período de tempo existente**.

   1. Escolha entre eventos **agregados** e **brutos**.

       > [!NOTE]
       > Você sempre pode agregar seus dados posteriormente em Power BI, mas não pode reverter para dados brutos após a agregação.

       > [!NOTE]
       > Há um limite de contagem de eventos de 250.000 para dados brutos de nível de evento.

       [![Connect](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Se você não tiver configurado seu ambiente de Azure Time Series Insights com **armazenamento quente**, receberá um aviso.

       [![Aviso de armazenamento quente](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Você pode configurar sua instância existente para **armazenamento quente** na portal do Azure.

1. Selecione **copiar consulta para a área de transferência**.
1. Agora, inicie o Power BI Desktop.
1. Em Power BI Desktop na guia **início** , selecione **obter dados** no canto superior esquerdo e, em seguida, **mais**.

    [![Menu suspenso início](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Procure **Azure Time Series insights**, selecione **Azure Time Series insights (beta)** e **Conecte-se**.

    [![Conectar Power BI ao Azure Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Como alternativa, navegue até a guia **Azure** , selecione **Azure Time Series insights (beta)** e **Conecte-se**.

1. Uma caixa de diálogo de mensagem será exibida solicitando permissão para se conectar a recursos de terceiros. Selecione **continuar**.

    [![Escolher criar consulta personalizada](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. No menu suspenso, em **fonte de dados**, escolha **criar consulta personalizada**. Cole da área de transferência no campo opcional de **consulta personalizada (opcional)** abaixo e pressione **OK**.

    [![Passe a consulta personalizada e selecione OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. A tabela de dados será carregada agora. Pressione **carregar** para carregar no Power bi.

    [![Examine os dados carregados na tabela e selecione carregar](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Se você tiver concluído essas etapas, pule para a próxima seção.

## <a name="create-a-report-with-visuals"></a>Criar um relatório com elementos visuais

Agora que você importou os dados para Power BI, é hora de criar um relatório com visuais.

1. No lado esquerdo da janela, verifique se você selecionou o modo de exibição de **relatório** .

    [![Captura de tela mostra o ícone de exibição de relatório.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. Na coluna **visualizações** , selecione seu visual de sua escolha. Por exemplo, selecione **gráfico de linhas**. Isso adicionará um gráfico de linhas em branco à tela.

1. Na lista **campos** , selecione **_Timestamp** e arraste-o para o campo **eixo** para exibir itens ao longo do eixo X. Certifique-se de mudar para **_Timestamp** como o valor do **eixo** (o padrão é a **hierarquia de data**).

    [![Captura de tela mostra o menu carimbo de data/hora com _Timestamp selecionado.](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1. Novamente, na lista **campos** , selecione **timeseriesid** e arraste-o para o campo **valores** para exibir itens ao longo do eixo Y.

    [![Criar um gráfico de linhas](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Para adicionar outro gráfico à tela, selecione qualquer lugar na tela fora do gráfico de linhas e repita esse processo.

    [![Criar gráficos adicionais para compartilhar](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Depois de criar o relatório, você poderá publicá-lo no Power BI Reporting Services.

## <a name="advanced-editing"></a>Edição avançada

Se você já carregou um conjunto de dados em Power BI mas deseja modificar a consulta (como os parâmetros de ID de ambiente ou de data/hora ou), você pode fazer isso por meio da funcionalidade de Editor Avançado do Power BI. Consulte a [documentação do Power bi](https://docs.microsoft.com/power-bi/desktop-query-overview) para saber mais.

Como uma visão geral:

1. Em Power BI Desktop, selecione **editar consultas**.
1. Pressione **Editor avançado**.

    [![Editar consultas no Editor Avançado](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Modifique a carga JSON conforme desejado.
1. Selecione **concluído** e **feche & aplicar** na janela do **Editor de Power Query**.

Agora, a interface refletirá as alterações desejadas que você aplicou.  

## <a name="next-steps"></a>Próximas etapas

* Leia sobre os [conceitos do conector de Power bi](https://docs.microsoft.com/power-bi/desktop-query-overview) para Azure Time Series insights.

* Saiba mais sobre o [Power bi desktop](https://docs.microsoft.com/power-bi/desktop-query-overview).
