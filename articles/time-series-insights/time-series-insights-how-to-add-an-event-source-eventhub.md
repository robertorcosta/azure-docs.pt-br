---
title: Adicionar uma origem de evento dos Hubs de Eventos ao Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como adicionar uma origem do evento que está conectada a Hubs de Eventos do Azure ao ambiente do Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: aaddfb19889e31bb8e0d52d1df2d6b034b6e7f6b
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274382"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Adicionar uma origem de evento do hub de eventos ao ambiente do Time Series Insights

Este artigo descreve como usar o portal do Azure para adicionar uma origem do evento que leia de um Hubs de Eventos do Azure para o ambiente do Azure Time Series Insights.

> [!NOTE]
> As etapas descritas neste artigo se aplicam aos ambientes Time Series Insights GA e Time Series Insights Preview.

## <a name="prerequisites"></a>Pré-requisitos

- Crie um ambiente de Time Series Insights conforme descrito em [criar um ambiente de Azure Time Series insights](./time-series-insights-update-create-environment.md).
- Crie um hub de eventos. Consulte [criar um namespace de hubs de eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md).
- O hub de eventos deve ter eventos de mensagem ativos enviados a ele. Saiba como [enviar eventos para os hubs de eventos do Azure usando o .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Crie um grupo de consumidores dedicado no hub de eventos do qual o ambiente do Time Series Insights possa consumir. Cada origem do evento do Time Series Insights deve ter seu próprio grupo de consumidores dedicado que não esteja compartilhado com nenhum outro consumidor. Se vários leitores consumirem eventos do mesmo grupo de consumidores, provavelmente, todos os leitores verão falhas. Há um limite de 20 grupos de consumidores por Hub de Eventos. Para obter detalhes, confira o [Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Adicionar um grupo de consumidores ao hub de eventos

Aplicativos usam grupos de consumidores para efetuar pull dos dados de Hubs de Eventos do Azure. Para ler dados de forma confiável do seu hub de eventos, forneça um grupo de consumidores dedicado que é usado somente por esse ambiente de Time Series Insights.

Para adicionar um novo grupo de consumidores ao seu hub de eventos:

1. Na [portal do Azure](https://portal.azure.com), localize e abra o Hub de eventos do namespace do hub de eventos.

    [![Open seu namespace do hub de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png#lightbox)

1. Em **Entidades**, selecione **Grupos de consumidores** e selecione **Grupo de Consumidores**.

   [Hub ![Event-adicionar um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png#lightbox)

1. Na página **Grupos de consumidores**, insira um novo valor exclusivo para **Nome**.  Use esse mesmo nome ao criar uma nova fonte de evento no ambiente do Time Series Insights.

1. Selecione **Criar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem do evento

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Localize seu ambiente Time Series Insights existente. No menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione o seu ambiente do Time Series Insights.

1. Em **Topologia do Ambiente**, selecione **Origens dos Eventos** e, em seguida, selecione **Adicionar**.

   [Fontes de evento ![Under, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

1. Insira um valor para **Nome da fonte do evento** que seja exclusivo para esse ambiente do Time Series Insights, como **fluxo-de-evento**.

1. Para **Origem**, selecione **Hub de Eventos**.

1. Selecione os valores apropriados para **Opção de importação**:

   * Se você tiver um hub de eventos existente em uma de suas assinaturas, selecione **Usar Hub de Eventos de assinaturas disponíveis**. Essa opção é a abordagem mais fácil.

     [![Select uma opção de importação de origem de evento](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png#lightbox)

    *  A tabela a seguir descreve as propriedades necessárias para a opção **Usar Hub de Eventos de assinaturas disponíveis**:

       [![Subscription e detalhes do hub de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png#lightbox)

       | Propriedade | DESCRIÇÃO |
       | --- | --- |
       | Assinatura | A assinatura à qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Namespace do Hub de Eventos | O namespace do hub de eventos ao qual a instância do hub de eventos desejada pertence. |
       | Nome do Hub de Eventos | O nome da instância do hub de eventos desejada. |
       | Valor da política do hub de eventos | Selecione a política de acesso compartilhado desejada. Você pode criar a política de acesso compartilhado na guia **Configurar** do hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura**. |
       | Chave de política do Hub de Eventos | Preenchido previamente com base no valor da política do hub de eventos selecionado. |

    * Se o hub de eventos for externo às suas assinaturas ou se você quiser selecionar opções avançadas, selecione **Fornecer configurações do Hub de Eventos manualmente**.

       A tabela a seguir descreve as propriedades necessárias para a opção **Fornecer configurações do Hub de Eventos manualmente**:
 
       | Propriedade | DESCRIÇÃO |
       | --- | --- |
       | ID da assinatura | A assinatura à qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Grupo de recursos | O grupo de recursos ao qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Namespace do Hub de Eventos | O namespace do hub de eventos ao qual a instância do hub de eventos desejada pertence. |
       | Nome do Hub de Eventos | O nome da instância do hub de eventos desejada. |
       | Valor da política do hub de eventos | Selecione a política de acesso compartilhado desejada. Você pode criar a política de acesso compartilhado na guia **Configurar** do hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura**. |
       | Chave de política do Hub de Eventos | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. Insira aqui a chave primária ou secundária. |

    * Ambas as opções compartilham as seguintes opções de configuração:

       | Propriedade | DESCRIÇÃO |
       | --- | --- |
       | Grupo de consumidores do Hub de Eventos | O grupo de consumidor que lê os eventos do hub de eventos. Recomendamos que você use um grupo de consumidores dedicado para a origem do evento. |
       | Formato de serialização do evento | No momento, JSON é único o formato de serialização disponível. As mensagens de evento devem estar nesse formato ou os dados não podem ser lidos. |
       | Nome da propriedade timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados de mensagem enviados para o hub de eventos. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de enfileiramento do evento** na origem do evento será usado como o carimbo de data/hora do evento. |

1. Adicione o nome de grupo de consumidores do Time Series Insights dedicado que você adicionou ao seu hub de eventos.

1. Selecione **Criar**.

   Depois que a origem do evento é criada, Time Series Insights automaticamente começa a transmitir dados para o seu ambiente.

## <a name="next-steps"></a>Próximas etapas

* [Definir as políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

* Acesse seu ambiente no [explorer do Time Series Insights](https://insights.timeseries.azure.com).
