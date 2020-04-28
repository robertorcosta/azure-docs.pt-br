---
title: Roteando eventos e mensagens – Azure digital gêmeos | Microsoft Docs
description: Visão geral de roteamento de eventos e mensagens de pontos de extremidade de serviço com os Gêmeos Digitais do Azure
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 65b760eaf28d907fab3654ed92f960be7556b0d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75862349"
---
# <a name="routing-iot-events-and-messages"></a>Roteando eventos e mensagens de IoT

Internet das Coisas soluções muitas vezes une vários serviços poderosos que incluem armazenamento, análise e muito mais. Este artigo descreve como conectar os aplicativos Gêmeos Digitais do Azure aos serviços analíticos, AI e de armazenamento do Azure para fornecer insight e funcionalidades mais profundas.

## <a name="route-types"></a>Tipos de rota  

O Azure digital gêmeos oferece duas maneiras de conectar eventos de IoT com outros serviços do Azure ou aplicativos de negócios:

* **Roteamento de eventos do Twins do Azure Digital**: um objeto no gráfico espacial que muda, dados de telemetria recebidos ou uma função definida pelo usuário que cria uma notificação com base em condições predefinidas pode acionar eventos do Gêmeos Digitais do Azure. Os usuários podem enviar esses eventos para [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/), [tópicos do Barramento de Serviço do Azure](https://azure.microsoft.com/services/service-bus/) ou [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) para processamento adicional.

* **Telemetria de dispositivo de roteamento**: além dos eventos de roteamentos, o recurso Gêmeos Digitais do Azure também pode rotear mensagens de telemetria brutas do dispositivo para os Hubs de Eventos para obter mais insights e análises. Esses tipos de mensagens não são processados pelo Gêmeos Digitais do Azure. E eles só estiverem encaminhados para o hub de eventos.

Os usuários podem especificar um ou mais pontos de extremidade de saída para enviar eventos ou encaminhar mensagens. Eventos e mensagens serão enviados aos pontos de extremidade de acordo com essas preferências de roteamentos predefinidas. Em outras palavras, os usuários podem especificar um determinado ponto de extremidade para receber eventos de operação de gráfico, outro para receber eventos de telemetria de dispositivo e assim por diante.

[![Roteamento de eventos do Gêmeos Digitais do Azure](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

Roteamento para Hubs de Eventos mantém a ordem na qual as mensagens de telemetria são enviadas. Então eles chegam ao ponto final na mesma sequência em que foram originalmente recebidos. 

A Grade de Eventos e o Barramento de Serviço do Microsoft Azure não garantem que os pontos de extremidade receberão eventos na mesma ordem em que ocorreram. No entanto, o esquema do evento inclui um registro de data e hora que pode ser usado para identificar a ordem após os eventos chegarem ao terminal.

## <a name="route-implementation"></a>Implementação de rota

O serviço de Gêmeos Digitais do Azure atualmente tem suporte para os seguintes **EndpointTypes**:

* **Hubs de eventos do Azure** é o ponto de extremidade de cadeia de caracteres de conexão de Hubs de eventos.
* **ServiceBus** é o ponto de extremidade de cadeia de conexão do barramento de serviço.
* **Grade de Eventos do Azure** é o ponto de extremidade de cadeia de caracteres de conexão de grade de eventos.

O recurso de Gêmeos Digitais do Azure atualmente tem suporte para os seguintes **EventTypes** que serão enviados para o ponto de extremidade escolhido:

* **DeviceMessages** são mensagens de telemetria enviadas dos dispositivos dos usuários e encaminhadas pelo sistema.
* **TopologyOperation** é uma operação que altera o gráfico ou os metadados do gráfico. Um exemplo é adicionar ou excluir uma entidade, como um espaço.
* **SpaceChange** é uma alteração no valor computado de um espaço que resulta de uma mensagem de telemetria de dispositivo.
* **SensorChange** é uma alteração no valor computado de um sensor que resulta de uma mensagem de telemetria do dispositivo.
* **UdfCustom** é uma notificação personalizada de uma função definida pelo usuário.

> [!IMPORTANT]  
> Nem todos os **EndpointTypes** dão suporte a todos **EventTypes**.
> Examine a tabela a seguir para obter os **EventTypes** permitidos para cada **ponto de extremidade**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Para obter mais informações sobre como criar pontos de extremidade e exemplos de esquema de eventos, leia a [saída e os pontos de extremidade](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os limites de visualização do Azure digital gêmeos, leia [limites de serviço de visualização pública](concepts-service-limits.md).

- Para experimentar um exemplo de gêmeos digital do Azure, leia o guia de [início rápido para localizar as salas disponíveis](quickstart-view-occupancy-dotnet.md).