---
title: Registros de diagnósticos do Azure Service Bus | Microsoft Docs
description: Este artigo fornece uma visão geral de todos os registros operacionais e diagnósticos disponíveis para o Azure Service Bus.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a80fb97810fee04a4eb50c43178c168e66f29173
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618723"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Habilite registros de diagnóstico saquepara ônibus de serviço

Quando você começar a usar o espaço de nome do Ônibus de Serviço do Azure, você pode querer monitorar como e quando seu namespace é criado, excluído ou acessado. Este artigo fornece uma visão geral de todos os registros operacionais e diagnósticos disponíveis.

Aazure Service Bus atualmente suporta registros operacionais e de atividade, que capturam *operações de gerenciamento* que são realizadas no espaço de nome do Ônibus de Serviço Azure. Especificamente, esses logs capturam o tipo de operação, incluindo a criação da fila, os recursos usados e o status da operação.

## <a name="operational-logs-schema"></a>Esquema de logs operacionais

Todos os logs são armazenados no formato JSON (JavaScript Object Notation, notação de objeto sinuoso) nos dois locais a seguir:

- **AzureActivity**: Exibe registros de operações e ações que são realizadas contra seu namespace no portal Azure ou através de implantações de modelo do Azure Resource Manager.
- **AzureDiagnostics**: Exibe registros de operações e ações que são conduzidas contra seu namespace usando a API ou através de clientes de gerenciamento no sdk de idioma.

As seqüências JSON de log operacional incluem os elementos listados na tabela a seguir:

| Nome | Descrição |
| ------- | ------- |
| ActivityId | ID interno, usado para identificar a atividade especificada |
| EventName | Nome da operação |
| ResourceId | ID de recurso do Azure Resource Manager |
| SubscriptionId | ID da assinatura |
| EventTimeString | Tempo de operação |
| EventProperties | Propriedades da operação |
| Status | Status da operação |
| Chamador | Chamador de operação (o portal Azure ou cliente de gerenciamento) |
| Categoria | OperationalLogs |

Este é um exemplo de uma cadeia de caracteres JSON do log operacional:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Eventos e operações capturados em registros operacionais

Os registros operacionais capturam todas as operações de gerenciamento que são executadas no espaço de nome do Ônibus de Serviço Do Azure. As operações de dados não são capturadas, devido ao alto volume de operações de dados que são realizadas no Ônibus de Serviço Azure.

> [!NOTE]
> Para ajudá-lo a rastrear melhor as operações de dados, recomendamos o uso do rastreamento do lado do cliente.

As seguintes operações de gerenciamento são capturadas em registros operacionais: 

| Escopo | Operação|
|-------| -------- |
| Namespace | <ul> <li> Criar um Namespace</li> <li> Atualizar namespace </li> <li> Excluir namespace </li> <li> Atualizar namespace sharedAccess Policy </li> </ul> | 
| Fila | <ul> <li> Criar fila</li> <li> Atualizar fila</li> <li> Excluir fila </li> <li> Fila de exclusão automática </li> </ul> | 
| Tópico | <ul> <li> Criar tópico </li> <li> Atualizar tópico </li> <li> Excluir tópico </li> <li> Tópico de exclusão automática </li> </ul> |
| Subscription | <ul> <li> Criar Assinatura </li> <li> Atualizar Assinatura </li> <li> Excluir Assinatura </li> <li> Assinatura de exclusão automática </li> </ul> |

> [!NOTE]
> Atualmente, as operações *read* não são rastreadas nos registros operacionais.

## <a name="enable-operational-logs"></a>Habilitar registros operacionais

Os registros operacionais são desativados por padrão. Para habilitar registros de diagnóstico, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com)vá para o espaço de nome do Ônibus de Serviço Do Azure e, em seguida, em **Monitoramento,** **selecione Configurações de diagnóstico**.

   ![O link "Configurações de diagnóstico"](./media/service-bus-diagnostic-logs/image1.png)

1. No painel **de configurações Diagnósticos,** selecione **Adicionar configuração de diagnóstico**.  

   ![O link "Adicionar configuração de diagnóstico"](./media/service-bus-diagnostic-logs/image2.png)

1. Configure as configurações de diagnóstico fazendo o seguinte:

   a. Na **caixa Nome,** digite um nome para as configurações de diagnóstico.  

   b. Selecione um dos três destinos a seguir para seus registros de diagnóstico:  
   - Se você selecionar **Arquivamento para uma conta de armazenamento,** você precisará configurar a conta de armazenamento onde os logs de diagnóstico serão armazenados.  
   - Se você selecionar **Stream para um hub de eventos,** você precisará configurar o hub de eventos para o que deseja transmitir os registros de diagnóstico.
   - Se você selecionar **Enviar para O Log Analytics,** você precisa especificar para qual instância do Log Analytics os diagnósticos serão enviados.  

   c. Selecione a caixa de seleção **OperationalLogs.**

    ![O painel "Configurações de diagnóstico"](./media/service-bus-diagnostic-logs/image3.png)

1. Clique em **Salvar**.

As novas configurações fazem efeito em cerca de 10 minutos. Os logs são exibidos no alvo de arquivamento configurado, no painel **logs diagnósticos.**

Para obter mais informações sobre a configuração das configurações de diagnóstico, consulte a [visão geral dos registros de diagnósticos do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Service Bus, consulte:

* [Introdução ao Barramento de Serviço](service-bus-messaging-overview.md)
* [Introdução ao Barramento de serviço](service-bus-dotnet-get-started-with-queues.md)
