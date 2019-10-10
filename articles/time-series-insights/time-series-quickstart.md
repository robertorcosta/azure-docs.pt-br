---
title: 'Início Rápido: Azure Time Series Insights Explorer | Microsoft Docs'
description: Este guia de início rápido mostra como começar a usar o gerenciador do Azure Time Series Insights no navegador da Web para visualizar grandes volumes de dados de IoT. Conheça os principais recursos em um ambiente de demonstração.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 09/23/2019
ms.openlocfilehash: 83446facc734c0bfa24b7e78916d652467d01415
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978691"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Início Rápido: Explorar o Azure Time Series Insights

Este guia de início rápido do gerenciador do Azure Time Series Insights ajuda você a começar a usar o Azure Time Series Insights Explorer em um ambiente de demonstração gratuito. Neste início rápido, você aprenderá a usar o navegador da Web para visualizar grandes volumes de dados de IoT e conhecerá os principais recursos atualmente em disponibilidade geral.

O Azure Time Series Insights é um serviço totalmente gerenciado de análise, armazenamento e visualização que facilita a exploração e análise de bilhões de eventos de IoT simultaneamente. Ele fornece uma exibição global dos dados, o que permite validar rapidamente sua solução de IoT e evitar um tempo de inatividade dispendioso de dispositivos críticos. O Azure Time Series Insights ajuda você a descobrir tendências ocultas, detectar anomalias e realizar análises de causa raiz quase em tempo real.

Para obter flexibilidade adicional, adicione o Azure Time Series Insights a um aplicativo já existente por meio de suas poderosas [APIs REST](./time-series-insights-update-tsq.md) e [SDK do cliente](https://github.com/microsoft/tsiclient). Você pode usar as APIs para armazenar, consultar e consumir dados de série temporal em um aplicativo cliente de sua escolha. Você também pode usar o SDK do cliente para adicionar componentes de interface do usuário ao aplicativo atual.

Este início rápido do gerenciador do Time Series Insights é um tour guiado de recursos que estão em disponibilidade geral.

> [!IMPORTANT]
> Criar uma [conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) se ainda não tiver criado uma.

## <a name="prepare-the-demo-environment"></a>Preparar o ambiente de demonstração

1. No navegador, vá até a [demonstração de disponibilidade geral](https://insights.timeseries.azure.com/demo).

1. Se solicitado, entre no gerenciador do Time Series Insights usando suas credenciais da conta do Azure.

1. A página de tour rápido do Time Series Insights será exibida. Escolha **Avançar** para iniciar o tour rápido.

   [![Escolher Avançar](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Explorar o ambiente de demonstração

1. O **Painel de seleção de tempo** é exibido. Use esse painel para selecionar um período a ser visualizado.

   [![Painel de seleção de tempo](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Escolha um período de tempo e arraste-o na região. Em seguida, escolha **Pesquisar**.

   [![Selecionar um período](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   O Time Series Insights exibe uma visualização de gráfico para o período especificado. Você pode realizar várias ações dentro do gráfico de linha. Por exemplo, você pode filtrar, fixar, classificar e empilhar.

   Para retornar ao **Painel de seleção de tempo**, escolha a seta para baixo, conforme mostrado:

   [![Gráfico](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Escolha **Adicionar** no **Painel de termos** para adicionar um novo termo de pesquisa.

   [![Adicionar item](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. No gráfico, você pode selecionar uma região, clicar com o botão direito do mouse na região e selecionar **Explorar Eventos**.

   [![Explorar Eventos](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Uma grade dos dados brutos é exibida na região que está sendo explorada.

   [![Exibição de grade](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Seleciona e filtra conjuntos de dados

1. Edite seus termos para alterar os valores no gráfico. Adicione outro termo para fazer uma correlação cruzada entre diferentes tipos de valores.

   [![Adicionar um termo](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Insira um termo de filtro na caixa **Filtrar série** para a filtragem improvisada da série. Para o guia de início rápido, insira **Station5** para fazer uma correlação cruzada entre a temperatura e a pressão dessa estação.

   [![Filtrar série](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Depois de concluir o guia de início rápido, faça experiências com o conjunto de dados de exemplo para criar visualizações diferentes.

## <a name="clean-up-resources"></a>Limpar recursos

Agora que concluiu o tutorial, limpe os recursos que você criou:

1. No menu à esquerda no [portal do Azure](https://portal.azure.com), selecione **Todos os recursos** e localize o grupo de recursos do Azure Time Series Insights.
1. Exclua todo o grupo de recursos (e todos os recursos contidos nele) selecionando **Excluir** ou remova cada recurso individualmente.

## <a name="next-steps"></a>Próximas etapas

Você está pronto para criar seu próprio ambiente do Time Series Insights:
> [!div class="nextstepaction"]
> [Planejar o ambiente do Time Series Insights](time-series-insights-environment-planning.md)
