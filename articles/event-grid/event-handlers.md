---
title: Manipuladores de eventos da Grade de Eventos do Azure
description: Descreve manipuladores de eventos suportados para a Azure Event Grid. Automação do Azure, Funções, Hubs de Eventos, Conexões Híbridas, Aplicativos lógicos, Barramento de Serviço, Armazenamento de Filas, Webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 73118823aec9b8d4bd872986b13f19496240c0b9
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393453"
---
# <a name="event-handlers-in-azure-event-grid"></a>Manipuladores de eventos na Grade de Eventos do Azure

Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa alguma ação adicional para processar o evento. Vários serviços do Azure são automaticamente configurados para manipular eventos. Você também pode usar qualquer WebHook para manipular eventos. O WebHook não precisa ser hospedado no Azure para manipular eventos. A Grade de Eventos dá suporte apenas a pontos de extremidade do WebHook HTTPS.

Este artigo fornece links para conteúdo para cada manipulador de eventos.

## <a name="azure-automation"></a>Automação do Azure

Use a Automação do Azure para processar eventos com runbooks automatizados.

|Title  |Descrição  |
|---------|---------|
|[Tutorial: Automação do Azure com Grade de Eventos e Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envia um evento. O evento dispara um runbook de Automação que marca a máquina virtual e dispara uma mensagem que é enviada para um canal do Microsoft Teams. |

## <a name="azure-functions"></a>Funções do Azure

Use o Azure Functions para respostas sem servidor aos eventos.

Ao usar o Azure Functions como o manipulador, use o gatilho Grade de Eventos em vez de gatilhos HTTP genéricos. A Grade de Eventos valida automaticamente os gatilhos de Função da Grade de Eventos. Com os gatilhos HTTP genéricos, você deve implementar a [resposta de validação](security-authentication.md#webhook-event-delivery).

|Title  |Descrição  |
|---------|---------|
| [Quickstart: Manuseie eventos com função](custom-event-to-function.md) | Envia um evento personalizado para uma função para processamento. |
| [Gatilho de Grade de Eventos para o Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Visão geral do uso do gatilho da Grade de Eventos no Functions. |
| [Tutorial: automatize redimensionar imagens enviadas usando event grid](resize-images-on-storage-blob-upload-event.md) | Os usuários fazem o upload de imagens por meio do aplicativo Web para a conta de armazenamento. Quando um blob de armazenamento é criado, a Grade de Eventos envia um evento para o aplicativo de função, que redimensiona a imagem carregada. |
| [Tutorial: transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md) | Quando os Hubs de Eventos criam um arquivo de Captura, a Grade de Eventos envia um evento para um aplicativo de função. O aplicativo recupera o arquivo de Captura e migra dados para um data warehouse. |
| [Tutorial: exemplos do Barramento de Serviço do Azure para a integração da Grade de Eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico do Barramento de Serviço para o aplicativo de função e o aplicativo lógico. |

## <a name="event-hubs"></a>Hubs de Eventos

Use o Hubs de Eventos quando sua solução receber eventos mais rápido do que é capaz de processá-los. Seu aplicativo processa os eventos dos Hubs de Eventos de acordo com sua própria agenda. Você pode dimensionar o processamento dos eventos para manipular os eventos de entrada.

Os Hubs de Eventos podem agir como uma fonte de evento ou um manipulador de eventos. O artigo a seguir mostra como usar os Hubs de Eventos como um manipulador.

|Title  |Descrição  |
|---------|---------|
| [Início Rápido: encaminhar eventos personalizados para os Hubs de Eventos do Azure com a CLI do Azure e a Grade de Eventos](custom-event-to-eventhub.md) | Envia um evento personalizado para um hub de eventos para processamento por um aplicativo. |
| [Modelo do Gerenciador de Recursos: tópico personalizado e ponto de extremidade de Hubs de Eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo do Gerenciador de Recursos que cria uma assinatura para um tópico personalizado. Envia eventos para os Hubs de Eventos do Azure. |

## <a name="hybrid-connections"></a>Conexões Híbridas

Use as Conexões Híbridas de Retransmissão do Azure para enviar eventos para aplicativos que estão em uma rede corporativa e não tem um ponto de extremidade publicamente acessível.

|Title  |Descrição  |
|---------|---------|
| [Tutorial: enviar eventos para conexão híbrida](custom-event-to-hybrid-connection.md) | Envia um evento personalizado para uma conexão híbrida existente para processamento por um aplicativo de escuta. |

## <a name="logic-apps"></a>Aplicativos Lógicos

Use aplicativos lógicos para automatizar processos de negócios para responder a eventos.

|Title  |Descrição  |
|---------|---------|
| [Tutorial: como monitorar alterações de máquina virtual com a Grade de Eventos do Azure e os aplicativos lógicos](monitor-virtual-machine-changes-event-grid-logic-app.md) | Um aplicativo lógico monitora as alterações feitas em uma máquina virtual e envia emails sobre essas alterações. |
| [Tutorial: enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos](publish-iot-hub-events-to-logic-apps.md) | Um aplicativo lógico envia um email de notificação sempre que um dispositivo é adicionado ao seu hub de IoT. |
| [Tutorial: exemplos do Barramento de Serviço do Azure para a integração da Grade de Eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico do Barramento de Serviço para o aplicativo de função e o aplicativo lógico. |

## <a name="service-bus"></a>Barramento de Serviço

### <a name="service-bus-queues"></a>Filas do Barramento de Serviço

Você pode direcionar eventos em Event Grid diretamente para filas de Ônibus de Serviço para uso em buffering ou comando & cenários de controle em aplicativos corporativos.

No portal Azure, ao criar uma assinatura de evento, selecione "Fila de Ponto de Serviço" como tipo de ponto final e clique em "selecionar um ponto final" para escolher uma fila de Ônibus de Serviço.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Usando a CLI para adicionar um manipulador de fila de ônibus de serviço

Para o Azure CLI, o exemplo a seguir se inscreve e conecta um tópico da grade de eventos a uma fila de Ônibus de Serviço:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

### <a name="service-bus-topics"></a>Tópicos do Barramento de Serviço

Você pode direcionar eventos em Event Grid diretamente para tópicos do Service Bus, a fim de lidar com eventos do sistema Azure com tópicos de Service Bus, ou para & cenários de mensagens de controle de comando.

No portal Azure, ao criar uma assinatura de evento, selecione "Service Bus Topic" como tipo de ponto final e clique em "selecionar e ponto final" para escolher um tópico de Barra de Serviço.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Usando a CLI para adicionar um manipulador de tópicos do Service Bus

Para o Azure CLI, o exemplo a seguir se inscreve e conecta um tópico da grade de eventos a uma fila de Ônibus de Serviço:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="queue-storage"></a>Armazenamento de Filas

Use Armazenamento de filas para receber eventos que precisam ser extraídos. Você pode usar o Armazenamento de Filas quando tem um processo de execução longa que demora muito para responder. Ao enviar eventos para o Armazenamento de Filas, o aplicativo pode receber e processar os eventos de acordo com a própria agenda.

|Title  |Descrição  |
|---------|---------|
| [Início Rápido: encaminhar eventos personalizados para o Armazenamento de Filas do Azure com a CLI do Azure e a Grade de Eventos](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um Armazenamento de filas. |

## <a name="webhooks"></a>WebHooks

Use webhooks para pontos de extremidade personalizáveis que respondem a eventos.

|Title  |Descrição  |
|---------|---------|
| Início Rápido: criar e encaminhar eventos personalizados com - [CLI do Azure](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md), e [portal](custom-event-quickstart-portal.md). | Mostra como enviar eventos personalizados para um WebHook. |
| Início Rápido: encaminhe eventos de armazenamento de Blob para um ponto de extremidade com - [CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) e [portal](blob-event-quickstart-portal.md). | Mostra como enviar eventos de armazenamento de blob para um WebHook. |
| [Início Rápido: enviar eventos de registro de contêiner](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar a CLI do Azure para enviar eventos de Registro de Contêiner. |
| [Visão geral: receber eventos em um ponto de extremidade HTTP](receive-events.md) | Descreve como validar um ponto de extremidade HTTP para receber eventos de uma Assinatura de Evento e depois receber e desserializar os eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
