---
title: Limites de serviço de pré-visualização pública - Azure Digital Twins | Microsoft Docs
description: Saiba mais sobre os limites de visualização pública, assinatura, instância e taxa para os Gêmeos Digitais Do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 5e323d8faa19ceb0712aa6183df17740ce2a0a1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370365"
---
# <a name="public-preview-service-limits"></a>Limites do serviço de visualização pública

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Durante a visualização pública, o Azure Digital Twins tem os seguintes limites temporários de assinatura, instância e taxa para os clientes existentes. Essas restrições existem para ajudar a simplificar o aprendizado sobre o novo serviço e seus muitos recursos, e serão aumentadas ou removidas pela disponibilidade geral (GA).

## <a name="per-subscription-limits"></a>Limites por assinatura

Durante a visualização pública, cada assinatura do Azure pode criar ou executar apenas uma instância dos Gêmeos Digitais do Azure por vez. Se você excluir sua instância, poderá criar uma nova.

## <a name="per-instance-limits"></a>Limites por instância

Por sua vez, cada instância de Gêmeos Digitais do Azure podem ter:

- Exatamente um recurso **IoTHub** inserido, que é criado automaticamente durante o provisionamento do serviço.
- Exatamente um ponto de extremidade **EventHub** para o tipo de evento **DeviceMessage**.
- Até três **EventHub**, **ServiceBus** ou **EventGrid** endpoints do tipo de evento **SensorChange**, **SpaceChange**, **TopologyOperation** ou **UdfCustom**.

> [!NOTE]
> Alguns parâmetros que geralmente são definidos ao criar as entidades de IoT do Azure acima não são necessários durante a versão prévia pública.
> - Veja a [documentação de referência do Swagger](./how-to-use-swagger.md) para as especificações de API mais recentes.

## <a name="azure-digital-twins-management-api-limits"></a>Limites de API de Gerenciamento dos Gêmeos Digitais do Azure

Os limites de taxa de solicitação para a API de Gerenciamento dos Gêmeos Digitais do Azure são:

- 100 solicitações por segundo para a API de Gerenciamento Digital dos Gêmeos Digitais do Azure.
- Até 1.000 objetos retornados por uma única consulta de API de Gerenciamento de Gêmeos Digitais do Azure.

> [!IMPORTANT]
> Se você exceder o limite de 1.000 objetos, receberá um erro e deverá simplificar sua consulta.

## <a name="user-defined-functions-rate-limits"></a>Limites de taxa de funções definidas pelo usuário

Os limites a seguir definem o número total de todas as chamadas de função definida pelo usuário feitas à sua instância de Gêmeos Digitais do Azure:

- 400 chamadas de biblioteca de cliente por segundo
- 100 chamadas **de sendnotification** por segundo

> [!NOTE]
> As ações a seguir podem fazer com que limites de taxa adicionais sejam aplicados temporariamente:
> - Edições feitas aos metadados do objeto de topologia
> - Atualizações feitas à definição de função definida pelo usuário
> - Dispositivos que enviam telemetria pela primeira vez

## <a name="device-telemetry-limits"></a>Limites de telemetria do dispositivo

Os limites a seguir limitam o número total de todas as mensagens que seus dispositivos podem enviar para sua instância dos Gêmeos Digitais do Azure:

- 100 mensagens por segundo em todos os dispositivos
-    25 mensagens por segundo por dispositivo

## <a name="next-steps"></a>Próximas etapas

- Para testar um exemplo de Gêmeos Digitais do Azure,vá para [Início Rápido para encontrar salas disponíveis](./quickstart-view-occupancy-dotnet.md).
