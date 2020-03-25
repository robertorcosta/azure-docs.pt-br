---
title: Exemplos de modelo do Azure Resource Manager – Grade de Eventos | Microsoft Docs
description: Este artigo fornece uma lista de amostras de modelo do Azure Resource Manager para a Grade de Eventos do Azure no GitHub.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 38d8db0bcc504760595fe51b63072f63e785577a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76720615"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modelos do Azure Resource Manager para Grade de Eventos

Para obter a sintaxe JSON e as propriedades a serem usadas em um modelo, confira [Tipos de recurso de Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). A tabela a seguir contém links para modelos do Azure Resource Manager para Grade de Eventos.

| | |
|-|-|
|**Assinaturas de Grade de Eventos**||
| [Tópico e assinatura personalizados com o ponto de extremidade do WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Implanta um tópico personalizado de Grade de Eventos. Cria uma assinatura para esse tópico personalizado que usa um ponto de extremidade do WebHook. |
| [Assinatura de tópico personalizada com o ponto de extremidade do EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Cria uma assinatura da Grade de Eventos para um tópico personalizado. A assinatura usa um Hub de Evento para o ponto de extremidade. |
| [Assinatura do Azure ou assinatura do grupo de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Assina eventos para um grupo de recursos ou assinatura do Azure. O grupo de recursos que você especificar como o de destino durante a implantação é a origem de eventos. A assinatura usa um WebHook para o ponto de extremidade. |
| [Conta de armazenamento de blobs e assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Implanta uma conta de armazenamento de blobs do Azure e assina eventos para essa conta de armazenamento. |
| | |
