---
title: Criar um ambiente – Azure Time Series Insights | Microsoft Docs
description: Saiba como usar o portal do Azure para criar um novo ambiente de Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7459c6afc775aa0df43d6f9285191c4c7e1b8cb8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81602359"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Criar um novo ambiente de Análise de Séries Temporais no portal do Azure

Este artigo descreve como criar um novo ambiente do Time Series Insights usando o portal do Azure.

O Time Series Insights permite que você comece a visualizar e a consultar dados que fluem para os Azure IoT Hubs e os Hubs de Eventos em minutos, permitindo que você consulte grandes volumes de dados de série de tempo em segundos.  Ele foi projetado para a escala da Internet das Coisas (IoT) e pode lidar com terabytes de dados.

## <a name="steps-to-create-the-environment"></a>Etapas para criar o ambiente

Siga estas etapas para criar um ambiente:

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione o botão **+ criar um recurso** .

1. Selecione a categoria **Internet das Coisas** e selecione **Time Series Insights**.

   [![Criar o ambiente de Análise de Séries Temporais](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Na página **Time Series Insights**, selecione **Criar**.

1. Preencha os parâmetros necessários. A tabela a seguir explica cada parâmetro:

   [![Criar o grupo de recursos de Análise de Séries Temporais](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Configuração|Valor sugerido|Descrição
   ---|---|---
   Nome do ambiente | Um nome exclusivo | Esse nome representa o ambiente no [explorer do Time Series](https://insights.timeseries.azure.com)
   Subscription | Sua assinatura | Se você tiver várias assinaturas, escolha a assinatura que contenha a origem do evento de preferência. Time Series Insights pode detectar automaticamente o Hub IoT do Azure e os recursos do hub de eventos existentes na mesma assinatura.
   Resource group | Criar um novo ou usar um existente | Um grupo de recursos é uma coleção de recursos do Azure que são usados juntos. Você pode escolher um grupo de recursos existente, como aquele que contém seu Hub de Eventos ou Hub IoT. Ou você pode criar um novo se esse recurso não estiver relacionado a outros recursos.
   Location | Mais próximo da origem do evento | Preferencialmente, escolha o mesmo local de centro de dados que contenha os dados de origem do evento, em um esforço para evitar os custos de largura de banda entre regiões e zonas e a latência adicional ao mover os dados para fora da região.
   Tipo de preço | S1 | Escolha a taxa de transferência necessária. Para ter custos mais baixos e a capacidade inicial, selecione S1.
   Capacity | 1 | A capacidade é o multiplicador aplicado à taxa de ingresso, à capacidade de armazenamento e ao custo associado com a SKU selecionada.  Você pode alterar a capacidade de um ambiente após a criação. Para custos mais baixos, selecione uma capacidade 1.
  
1. Selecione **Criar** para iniciar o processo de provisionamento. Isso poderá levar alguns minutos.

1. Para monitorar o processo de implantação, selecione o símbolo **Notificações** (ícone de sino).

   [![Assistir às notificações](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. Na **visão geral**do recurso, verifique as definições de configuração da implantação.

   [![Criar uma marcação para Análise de Séries Temporais no painel](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Opcional)** Selecione o **ícone de pino** no canto superior direito para acessar facilmente seu ambiente de time Series insights no futuro.

## <a name="next-steps"></a>Próximas etapas

* [Defina as políticas de acesso de dados](time-series-insights-data-access.md) para proteger seu ambiente.

* [Adicione uma origem de evento do Hub de Eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) ao ambiente do Azure Time Series Insights.

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

* Exiba seu ambiente no [Time Series insights Explorer](https://insights.timeseries.azure.com).
