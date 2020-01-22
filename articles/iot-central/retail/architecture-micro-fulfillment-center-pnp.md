---
title: Microcentral de atendimento do Azure IoT Central | Microsoft Docs
description: Aprenda a criar um aplicativo de microcentral de atendimento usando nosso modelo de aplicativo de microcentral de atendimento no IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35f99abaf5e0142c29d6dd43c968b66b21a28a50
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898521"
---
# <a name="micro-fulfillment-center-architecture"></a>Arquitetura da microcentral de atendimento

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

As soluções de microcentral de atendimento permitem que você conecte, monitore e gerencie digitalmente todos os aspectos de uma central de atendimento totalmente automatizada, a fim de reduzir custos ao eliminar o tempo de inatividade e aumentar simultaneamente a segurança e a eficiência geral. Essas soluções podem ser construídas aproveitando um dos modelos de aplicativos no IoT Central e a arquitetura abaixo como orientação.

![Análise do repositório do Azure IoT Central](./media/architecture/micro-fulfillment-center-architecture-frame.png)

- Conjunto de sensores de IoT que enviam dados de telemetria para um dispositivo de gateway
- Dispositivos de gateway que enviam telemetria e informações agregadas para o IoT Central
- Exportação contínua de dados para manipulação pelo serviço do Azure desejado
- Os dados podem ser estruturados no formato desejado e enviados para um serviço de armazenamento
- Os aplicativos de negócios podem consultar dados e gerar informações que potencializam as operações de varejo
 
Vamos dar uma olhada nos principais componentes que geralmente desempenham um papel em uma solução de microcentral de atendimento.

## <a name="robotic-carriers"></a>Operadoras robóticas

Uma solução de microcentral de atendimento provavelmente terá um grande conjunto de operadoras de robótica, gerando uma variedade de sinais de telemetria. Esses sinais podem ser ingeridos por um dispositivo de gateway, agregados e enviados para o IoT Central conforme demonstrado no lado esquerdo do diagrama de arquitetura.  

## <a name="condition-monitoring-sensors"></a>Sensores de monitoramento de condição

Uma solução de IoT começa com um conjunto de sensores que coletam sinais significativos de dentro da sua central de atendimento. Isso é refletido por uma variedade de sensores na extremidade esquerda do diagrama de arquitetura mostrada acima.

## <a name="gateway-devices"></a>Dispositivos de gateway

Muitos sensores de IoT podem alimentar sinais brutos diretamente na nuvem ou em um dispositivo de gateway localizado perto deles. O dispositivo de gateway executa a agregação de dados na borda antes de enviar informações de resumo para um aplicativo IoT Central. Os dispositivos de gateway também são responsáveis por retransmitir as operações de comando e controle para os dispositivos sensores, quando aplicável. 

## <a name="iot-central-application"></a>Aplicativo IoT Central

O aplicativo Azure IoT Central ingere dados de uma variedade de sensores de IoT, robôs e dispositivos de gateway no ambiente da central de atendimento e gera um conjunto de insights significativos.

O Azure IoT Central também fornece uma experiência personalizada para os operadores da loja, permitindo que eles monitorem e gerenciem remotamente os dispositivos de infraestrutura.

## <a name="data-transform"></a>Transformação de dados
O aplicativo Azure IoT Central dentro de uma solução pode ser configurado para exportar insights brutos ou agregados para um conjunto de serviços PaaS do Azure (Plataforma como Serviço) que pode executar a manipulação de dados e enriquecer esses insights antes de destiná-los para um aplicativo de negócios. 

## <a name="business-application"></a>Aplicativo de negócios
Os dados da IoT podem ser usados para alimentar uma variedade de aplicativos de negócios implantados em um ambiente de varejo. Um gerente ou funcionário de uma central de atendimento pode usar esses aplicativos para visualizar informações de negócios e executar ações significativas em tempo real. Para aprender a criar um dashboard do Power BI em tempo real para sua equipe de varejo, siga este [tutorial](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="next-steps"></a>Próximas etapas
* Introdução ao modelo de aplicativo da [microcentral de atendimento](https://aka.ms/checkouttemplate). 
* Dê uma olhada no [tutorial](https://aka.ms/mfc-tutorial) que guia você passo a passo pela criação de uma solução utilizando o modelo de aplicativo da microcentral de atendimento.
