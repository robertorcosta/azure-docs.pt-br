---
title: Entender os pontos de extremidade do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor ‑ Informações de referência sobre pontos de extremidade do Hub IoT voltados para o dispositivo e para o serviço.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 53660ad93ab2218d546ae6f363873c4d66872e2b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730302"
---
# <a name="reference---iot-hub-endpoints"></a>Referência - Pontos de extremidade do Hub IoT

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Nomes de Hub IoT

É possível localizar o nome do host do Hub IoT que hospeda os pontos de extremidade no portal na página de **Visão Geral** do hub. Por padrão, o nome DNS de um Hub IoT parece com: `{your iot hub name}.azure-devices.net`.

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista de pontos de extremidade internos do Hub IoT

O Hub IoT do Azure é um serviço multilocatário que expõe suas funcionalidades a vários atores. O diagrama a seguir mostra os diversos pontos de extremidade que o Hub IoT expõe.

![Pontos de extremidade do Hub IoT](./media/iot-hub-devguide-endpoints/endpoints.png)

A lista a seguir descreve os pontos de extremidade:

* **Provedor de recursos**. O provedor de recursos de Hub IoT expõe uma interface do [Azure Resource Manager](../azure-resource-manager/management/overview.md). Essa interface permite que proprietários de assinatura do Azure criem e excluam Hubs IoT e atualizem as propriedades de Hub IoT. As propriedades do Hub IoT regem as [políticas de segurança no nível do hub](iot-hub-devguide-security.md#access-control-and-permissions), ao contrário do controle de acesso no nível do dispositivo e das opções funcionais para mensagens da nuvem para dispositivo e do dispositivo para nuvem. O provedor de recursos do Hub IoT também permite [exportar identidades do dispositivo](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Gerenciamento de identidades dos dispositivos**. Cada Hub IoT expõe um conjunto de pontos de extremidade HTTPS REST para o gerenciamento de identidades do dispositivo (criar, recuperar, atualizar e excluir). [As identidades dos dispositivos](iot-hub-devguide-identity-registry.md) são usadas para autenticação de dispositivos e controle de acesso.

* **Gerenciamento do dispositivo gêmeo**. Cada Hub IoT expõe um conjunto de pontos de extremidade REST HTTPS voltado para o serviço para consultar e atualizar [dispositivos gêmeos](iot-hub-devguide-device-twins.md) (atualizar marcas e propriedades).

* **Gerenciamento de trabalhos**. Cada Hub IoT expõe um conjunto de ponto de extremidade REST HTTPS voltado para o serviço para consultar e gerenciar [trabalhos](iot-hub-devguide-jobs.md).

* **Pontos de extremidade do dispositivo**. Para cada dispositivo no registro de identidade, o Hub IoT expõe um conjunto de pontos de extremidade:

  * *Envie mensagens de dispositivo para nuvem*. Um dispositivo usa esse ponto de extremidade para [enviar mensagens do dispositivo para nuvem](iot-hub-devguide-messages-d2c.md).

  * *Receba mensagens cloud-to-device*. Um dispositivo usa esse ponto de extremidade para receber [mensagens da nuvem para dispositivo direcionadas](iot-hub-devguide-messages-c2d.md).

  * *Inicie os uploads de arquivos*. Um dispositivo usa esse ponto de extremidade para receber um URI de SAS do Armazenamento do Azure do Hub IoT para [carregar um arquivo](iot-hub-devguide-file-upload.md).

  * *Recupere e atualize as propriedades do dispositivo gêmeo*. Um dispositivo usa esse ponto de extremidade para acessar as propriedades do [dispositivo gêmeo](iot-hub-devguide-device-twins.md).

  * *Receber solicitações de métodos diretos*. Um dispositivo usa esse ponto de extremidade para escutar as solicitações do [método direto](iot-hub-devguide-direct-methods.md).

    Esses pontos de extremidade são expostos usando os protocolos [MQTT v3.1.1](https://mqtt.org/), HTTPS 1.1 e [AMQP 1.0](https://www.amqp.org/). O AMQP também está disponível sobre [WebSockets](https://tools.ietf.org/html/rfc6455) na porta 443.

* **Pontos finais de serviço**. Cada Hub IoT expõe um conjunto de pontos de extremidade para que o seu back-end da sua solução se comunique com os seus dispositivos. Com uma exceção, esses pontos de extremidade só são expostos usando o protocolo [AMQP](https://www.amqp.org/). O ponto de extremidade de invocação de método é exposto através do protocolo HTTPS.
  
  * *Receba mensagens dispositivo-nuvem*. Esse ponto de extremidade é compatível com [Hubs de Eventos do Azure](https://azure.microsoft.com/documentation/services/event-hubs/). Um serviço de back-end pode usá-lo para ler as [mensagens do dispositivo para nuvem](iot-hub-devguide-messages-d2c.md) enviadas por seus dispositivos. Você pode criar pontos de extremidade personalizados em seu hub IoT, além desse ponto de extremidade interno.
  
  * *Enviar mensagens da nuvem para o dispositivo e receber confirmações de entrega*. Esses pontos de extremidade permitem que o back-end da solução envie [mensagens da nuvem para dispositivo](iot-hub-devguide-messages-c2d.md) confiáveis e receba as confirmações de entrega ou de expiração correspondentes.
  
  * *Receba notificações de arquivo*. Esse ponto de extremidade de mensagens permite que você receba notificações quando os dispositivos carregarem com êxito um arquivo. 
  
  * *Invocação direta de método*. Esse ponto de extremidade permite que um serviço de back-end invoque um [método direto](iot-hub-devguide-direct-methods.md) em um dispositivo.
  
  * *Eventos de monitoramento de operações de recebimento*. Esse ponto de extremidade permitirá que você receba eventos de monitoramento de operações se o hub IoT tiver sido configurado para emiti-los. Para obter mais informações, confira o [Monitoramento de operações do Hub IoT](iot-hub-operations-monitoring.md).

O artigo [SDKs do Azure IoT](iot-hub-devguide-sdks.md) descreve as várias maneiras de acessar esses pontos de extremidade.

Todos os pontos de extremidade do Hub IoT usam o protocolo [TLS](https://tools.ietf.org/html/rfc5246) e nenhum ponto de extremidade é exposto em canais sem criptografia/desprotegidos.

## <a name="custom-endpoints"></a>Pontos de extremidade personalizados

Você pode vincular os serviços existentes do Azure em sua assinatura ao Hub IoT para serem usados como pontos de extremidade no direcionamento de mensagens. Esses agem como pontos de extremidade de serviço e são usados como "coletores" para rotas de mensagens. Os dispositivos não podem gravar diretamente nos pontos de extremidade adicionais. Saiba mais sobre o [roteamento de mensagens](../iot-hub/iot-hub-devguide-messages-d2c.md).

Atualmente, o Hub IoT é compatível com os seguintes serviços do Azure como pontos de extremidade adicionais:

* Contêineres de Armazenamento do Azure
* Hubs de Eventos
* Filas de barramento de serviço
* Tópicos do Service Bus

Para saber quais são os limites para o número de pontos de extremidade que você pode adicionar, confira [Cotas e limitação](iot-hub-devguide-quotas-throttling.md).

Você pode usar a API REST [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) para obter o estado de saúde dos pontos finais. Recomendamos o uso das métricas do [IoT Hub relacionadas](iot-hub-metrics.md) à latência de mensagens de roteamento para identificar e depurar erros quando a saúde do ponto final estiver morta ou insalubre, pois esperamos que a latência seja maior quando o ponto final estiver em um desses estados.

|Status de integridade|Descrição|
|---|---|
|Healthy|O ponto final é aceitar mensagens como esperado.|
|Insalubre|O ponto final é não aceitar mensagens como esperado e o IoT Hub está tentando novamente enviar dados para este ponto final. O status de um ponto final insalubre será atualizado para saudável quando o IoT Hub estabelecer um estado de saúde eventualmente consistente.|
|unknown|O IoT Hub não estabeleceu uma conexão com o ponto final. Nenhuma mensagem foi entregue ou rejeitada a partir deste ponto final.|
|Morto|O ponto final é não aceitar mensagens, depois que o IoT Hub tentou novamente enviar mensagens para o período de novo julgamento.|

## <a name="field-gateways"></a>Gateways de campo

Em uma solução IoT, um *gateway de campo* fica entre os dispositivos e os pontos de extremidade do hub IoT. Normalmente, ele se localiza perto dos dispositivos. Os dispositivos se comunicam diretamente com o gateway de campo usando um protocolo com suporte dos dispositivos. O gateway de campo conecta-se com um ponto de extremidade Hub IoT usando um protocolo que tem suporte do Hub IoT. Um gateway de campo pode ser um dispositivo de hardware dedicado ou em um computador de baixa capacidade que executam o software do gateway personalizado.

Você pode usar o [Azure IoT Edge](/azure/iot-edge/) para implementar um gateway de campo. O IoT Edge oferece funcionalidades como a multiplexação de comunicações de vários dispositivos na mesma conexão do Hub IoT.

## <a name="next-steps"></a>Próximas etapas

Outros tópicos de referência neste Guia do desenvolvedor do Hub IoT incluem:

* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md)
* [Cotas e limitação](iot-hub-devguide-quotas-throttling.md)
* [Suporte ao MQTT do Hub IoT](iot-hub-mqtt-support.md)
* [Entenda seu endereço IP do hub de IoT](iot-hub-understand-ip-address.md)