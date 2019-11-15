---
title: Arquitetura da análise na loja
description: Aprenda a criar um aplicativo de análise de loja usando o modelo de aplicativo de finalização de compra no IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 728c5dcbf94e030e860a222add6498824c66930d
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615392"
---
# <a name="in-store-analytics-architecture"></a>Arquitetura de análise na loja

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

As soluções de análise na loja permitem monitorar várias condições no ambiente da loja de varejo. Essas soluções podem ser construídas aproveitando um dos modelos de aplicativos no IoT Central e a arquitetura abaixo como orientação.


![Análise do repositório do Azure IoT Central](./media/architecture/store-analytics-architecture-frame.png)

- Conjunto de sensores de IoT que enviam dados de telemetria para um dispositivo de gateway
- Dispositivos de gateway que enviam telemetria e informações agregadas para o IoT Central
- Exportação contínua de dados para manipulação pelo serviço do Azure desejado
- Os dados podem ser estruturados no formato desejado e enviados para um serviço de armazenamento
- Os aplicativos de negócios podem consultar dados e gerar informações que potencializam as operações de varejo
 
Vamos ver os principais componentes que geralmente desempenham um papel em uma solução de análise em repositório.

## <a name="condition-monitoring-sensors"></a>Sensores de monitoramento de condição

Uma solução de IoT começa com um conjunto de sensores que coletam sinais significativos de dentro de um ambiente de loja de varejo. Isso é refletido por uma variedade de sensores na extremidade esquerda do diagrama de arquitetura mostrada acima.

## <a name="gateway-devices"></a>Dispositivos de gateway

Muitos sensores de IoT podem alimentar sinais brutos diretamente na nuvem ou em um dispositivo de gateway localizado perto deles. O dispositivo de gateway executa a agregação de dados na borda antes de enviar informações de resumo para um aplicativo IoT Central. Os dispositivos de gateway também são responsáveis por retransmitir as operações de comando e controle para os dispositivos sensores, quando aplicável. 

## <a name="iot-central-application"></a>Aplicativo IoT Central

O aplicativo Azure IoT Central ingere dados de uma variedade de sensores de IoT, bem como de dispositivos de gateway, no ambiente da loja de varejo e gera um conjunto de informações significativas.

O Azure IoT Central também fornece uma experiência personalizada para os operadores da loja, permitindo que eles monitorem e gerenciem remotamente os dispositivos de infraestrutura.

## <a name="data-transform"></a>Transformação de dados
O aplicativo Azure IoT Central dentro de uma solução pode ser configurado para exportar informações brutas ou agregadas para um conjunto de serviços PaaS do Azure (Plataforma como Serviço) que pode executar a manipulação de dados e enriquecer essas informações antes de destiná-las para um aplicativo comercial. 

## <a name="business-application"></a>Aplicativo de negócios
Os dados da IoT podem ser usados para alimentar uma variedade de aplicativos de negócios implantados em um ambiente de varejo. Um gerente de loja de varejo ou membro da equipe pode utilizar esses aplicativos para visualizar informações de negócios e executar ações significativas em tempo real. Para aprender a criar um painel do Power BI em tempo real para sua equipe de varejo, siga este [tutorial](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="next-steps"></a>Próximas etapas
* Comece com os modelos de aplicativo [Análise de finalização de compras na loja](https://aka.ms/checkouttemplate) e [Monitoramento de condições de análises na loja](https://aka.ms/conditiontemplate). 
* Confira o [tutorial de ponta a ponta](https://aka.ms/storeanalytics-tutorial), que explica como criar uma solução usando um dos modelos de aplicativo de Análise na Loja.
