---
title: Adicione uma fonte de eventos do Event Hubs - Azure Time Series Insights | Microsoft Docs
description: Saiba como adicionar uma fonte de evento do Hubs de Eventos do Azure ao ambiente do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 021ac5fccf4d694895ab9941bd46dd2388f49af9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407451"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Adicionar uma origem de evento do hub de eventos ao ambiente do Time Series Insights

Este artigo descreve como usar o portal do Azure para adicionar uma origem do evento que leia de um Hubs de Eventos do Azure para o ambiente do Azure Time Series Insights.

> [!NOTE]
> As etapas descritas neste artigo aplicam-se tanto aos ambientes de visualização de insights da série do tempo quanto de insights de séries tempo.

## <a name="prerequisites"></a>Pré-requisitos

- Crie um ambiente de insights de séries tempoviárias conforme descrito no [criar um ambiente de insights da série de tempo do Azure](./time-series-insights-update-create-environment.md).
- Crie um hub de eventos. Leia [Criar um namespace do Event Hubs e um hub de eventos usando o portal Azure](../event-hubs/event-hubs-create.md).
- O hub de eventos deve ter eventos de mensagem ativos enviados a ele. Saiba como [enviar eventos para o Azure Event Hubs usando o .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Crie um grupo de consumidores dedicado no hub de eventos do qual o ambiente do Time Series Insights possa consumir. Cada origem do evento do Time Series Insights deve ter seu próprio grupo de consumidores dedicado que não esteja compartilhado com nenhum outro consumidor. Se vários leitores consumirem eventos do mesmo grupo de consumidores, todos os leitores provavelmente apresentarão falhas. Há um limite de 20 grupos de consumidores por Hub de Eventos. Para mais detalhes, leia o [guia de programação do Event Hubs.](../event-hubs/event-hubs-programming-guide.md)

### <a name="add-a-consumer-group-to-your-event-hub"></a>Adicionar um grupo de consumidores ao hub de eventos

Aplicativos usam grupos de consumidores para efetuar pull dos dados de Hubs de Eventos do Azure. Para ler com confiança os dados do seu hub de eventos, forneça um grupo de consumidores dedicado que é usado apenas por este ambiente Time Series Insights.

Para adicionar um novo grupo de consumidores ao seu hub de eventos:

1. No [portal Azure,](https://portal.azure.com)localize e abra a instância do hub de eventos a partir do painel **Visão Geral** do namespace do seu hub de eventos. Selecione **Entidades > Hubs de Eventos** ou encontre sua instância em **Nome**.

    [![Abra seu espaço de nome para hub de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Na sua instância de hub de eventos, selecione **Entidades > Grupos de Consumidores**. Em seguida, selecione **+ Grupo consumidor** para adicionar um novo grupo de consumidores. 

   [![Hub de eventos - Adicionar um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Caso contrário, selecione um grupo de consumidores existente e pule para a próxima seção.

1. Na página **Grupos de consumidores**, insira um novo valor exclusivo para **Nome**.  Use esse mesmo nome ao criar uma nova fonte de evento no ambiente do Time Series Insights.

1. Selecione **Criar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem do evento

1. Entre no [portal do Azure](https://portal.azure.com).

1. Localize seu ambiente Time Series Insights existente. No menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione o seu ambiente do Time Series Insights.

1. Selecione **Fontes de**evento e selecione **Adicionar**.

   [![Em Fontes de Evento, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Digite um valor para **nome de origem do evento** que `Contoso-TSI-GA-Event-Hub-ES`é exclusivo para este ambiente Time Series Insights, como .

1. Para **Origem**, selecione **Hub de Eventos**.

1. Selecione os valores apropriados para **Opção de importação**:

   * Se você tiver um hub de eventos existente em uma de suas assinaturas, selecione **Usar Hub de Eventos de assinaturas disponíveis**. Essa opção é a abordagem mais fácil.

     [![Selecione uma opção de importação de origem de evento](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  A tabela a seguir descreve as propriedades necessárias para a opção **Usar Hub de Eventos de assinaturas disponíveis**:

       [![Detalhes da assinatura e do hub de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Propriedade | Descrição |
       | --- | --- |
       | Subscription | A assinatura da instância de hub de eventos desejada e do namespace pertence. |
       | Namespace do Hub de Eventos | O namespace do hub de eventos a instância desejada do hub de eventos pertence. |
       | Nome do Hub de Eventos | O nome da instância de hub de eventos desejada. |
       | Valor da política do Event Hub | Selecione a política de acesso compartilhado desejada. Você pode criar a política de acesso compartilhado na guia **Configurar** o hub de eventos. Cada política de acesso compartilhado tem um nome, permissões que você define e chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura**. |
       | Chave de política do Hub de Eventos | Pré-preenchido a partir do valor de política do Event Hub selecionado. |

    * Se o hub de eventos for externo às suas assinaturas ou se você quiser selecionar opções avançadas, selecione **Fornecer configurações do Hub de Eventos manualmente**.

       A tabela a seguir descreve as propriedades necessárias para a opção **Fornecer configurações do Hub de Eventos manualmente**:
 
       | Propriedade | Descrição |
       | --- | --- |
       | ID da assinatura | A assinatura da instância de hub de eventos desejada e do namespace pertence. |
       | Resource group | O grupo de recursos a instância de hub de eventos desejado e namespace pertence. |
       | Namespace do Hub de Eventos | O namespace do hub de eventos a instância desejada do hub de eventos pertence. |
       | Nome do Hub de Eventos | O nome da instância de hub de eventos desejada. |
       | Valor da política do Event Hub | Selecione a política de acesso compartilhado desejada. Você pode criar a política de acesso compartilhado na guia **Configurar** o hub de eventos. Cada política de acesso compartilhado tem um nome, permissões que você define e chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura**. |
       | Chave de política do Hub de Eventos | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. Insira aqui a chave primária ou secundária. |

    * Ambas as opções compartilham as seguintes opções de configuração:

       | Propriedade | Descrição |
       | --- | --- |
       | Grupo de consumidores do Hub de Eventos | O grupo de consumidor que lê os eventos do hub de eventos. Recomendamos que você use um grupo de consumidores dedicado para a origem do evento. |
       | Formato de serialização do evento | No momento, JSON é único o formato de serialização disponível. As mensagens de evento devem estar neste formato ou os dados não podem ser lidos. |
       | Nome da propriedade timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados de mensagem enviados para o hub de eventos. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de enfileiramento do evento** na origem do evento será usado como o carimbo de data/hora do evento. |

1. Adicione o nome de grupo de consumidores do Time Series Insights dedicado que você adicionou ao seu hub de eventos.

1. Selecione **Criar**.

   Depois que a fonte do evento é criada, o Time Series Insights começa automaticamente a transmitir dados para o seu ambiente.

## <a name="next-steps"></a>Próximas etapas

* [Definir as políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.

* [Envie eventos](time-series-insights-send-events.md) para a fonte do evento.

* Acesse seu ambiente no [explorer do Time Series Insights](https://insights.timeseries.azure.com).
