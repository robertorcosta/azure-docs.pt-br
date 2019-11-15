---
title: Arquitetura de logística conectada para IoT | Microsoft Docs
description: Arquitetura do modelo de aplicativo de logística conectada para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 1940f02fdf9badcf99ef85a2ab02ac9099cc1775
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615389"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Arquitetura do modelo de aplicativo de logística conectada para IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Os parceiros e clientes podem aproveitar o modelo de aplicativo e as diretrizes a seguir para desenvolver **soluções de logística conectada** de ponta a ponta.

> [!div class="mx-imgBorder"]
> ![painel de logística conectada](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Conjunto de marcas de IoT que enviam dados de telemetria para um dispositivo de gateway
2. Dispositivos de gateway que enviam telemetria e informações agregadas para o IoT Central
3. Os dados são roteados para o serviço do Azure desejado para fins de manipulação
4. Os serviços do Azure, como ASA ou Azure Functions, podem ser aproveitados para reformatar os fluxos de dados e enviar para as contas de armazenamento desejadas 
5. Os aplicativos de negócios do usuário final podem alimentar vários fluxos de trabalho de negócios.

## <a name="details"></a>Detalhes
A seção a seguir descreve cada parte da ingestão de telemetria da arquitetura conceitual de marcas e gateways de IoT.

## <a name="iot-tags"></a>Marcas de IoT
As marcas de IoT fornecem funcionalidades de sensor físico e ambiental, como temperatura, umidade, impacto, inclinação e luz. Normalmente, as marcas de IoT se conectam a dispositivos de gateway por meio de um Zigbee (802.15.4). As marcas são sensores econômicos, portanto, eles podem ser descartados no final de uma jornada de logística típica a fim de evitar problemas com logística reversa.

## <a name="gateway"></a>Gateway
Os gateways também podem atuar como marcas de IoT com as respectivas funcionalidades de sensor de ambientes. O gateway habilita a conectividade de nuvem (MQTT) ascendente do Azure IoT por meio dos canais de celular e Wi-Fi.  Os modos Wireless Sensor Network (WSN) 802.15.4, Bluetooth e NFC servem para comunicação downstream com marcas IoT. Os gateways fornecem conectividade de nuvem segura de ponta a ponta, emparelhamento de marcas de IoT, agregação de dados de sensor, retenção de dados e capacidade de configurar limites de alarme.

## <a name="device-management-with-iot-central"></a>Gerenciamento de dispositivos com IoT Central 
O Azure IoT Central é uma plataforma de desenvolvimento de soluções que simplifica a conectividade, a configuração e o gerenciamento de dispositivos IoT. A plataforma reduz significativamente a carga e os custos de gerenciamento, operações e desenvolvimentos relacionados de dispositivos IoT. Os clientes e parceiros podem criar soluções empresariais de ponta a ponta para obter um loop de comentários digital sobre as operações logísticas.

## <a name="business-insights--actions-via-data-egress"></a>Insight de negócios & ações por meio da saída de dados 
A plataforma IoT Central fornece opções avançadas de extensibilidade por meio de CDE (Exportação de Dados Contínua) e APIs. Os insights de negócios baseados no processamento de dados de telemetria ou na telemetria bruta são exportadas normalmente para um aplicativo de linha de negócios preferencial. Isso pode ser obtido por meio de webhook, barramento de serviço, hub de eventos ou armazenamento de blob para criar, treinar e implantar modelos de aprendizado de máquina e enriquecer ainda mais os insights.

## <a name="next-steps"></a>Próximas etapas
* Aprenda a implantar um [modelo de solução de logística conectada](./tutorial-iot-central-connected-logistics-pnp.md).
* Saiba mais sobre [modelos comerciais do IoT Central](./overview-iot-central-retail-pnp.md)
* Para saber mais sobre o IoT Central, confira [Visão geral do IoT Central](../core/overview-iot-central-pnp.md)
