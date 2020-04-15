---
title: Pré-requisitos & componentes
description: Detalhes de tudo o que é necessário para começar com o Azure Security Center para pré-requisitos de serviço de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: fb5c42ad490ed04f14ff150093a44b552434ed9e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310577"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Centro de Segurança Azure para pré-requisitos de IoT

Este artigo fornece uma explicação dos diferentes componentes do Azure Security Center para o serviço de IoT, o que você precisa começar, e explica os conceitos básicos para ajudar a entender o serviço.

## <a name="minimum-requirements"></a>Requisitos mínimos

- Nível padrão do IoT Hub
  - Privilégios **de** nível de proprietário do RBAC
- [Espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Centro de Segurança Azure (recomendado)
  - O uso do Azure Security Center é uma recomendação, e não um requisito. Sem o Azure Security Center, você não poderá visualizar seus outros recursos do Azure no IoT Hub.

## <a name="working-with-azure-security-center-for-iot-service"></a>Trabalhando com o Azure Security Center para serviço de IoT

O Azure Security Center para insights e relatórios de IoT está disponível usando o Azure IoT Hub e o Azure Security Center. Para habilitar o Azure Security Center para IoT no seu Hub IoT Do Zure, é necessária uma conta com privilégios de nível **de proprietário.** Depois de habilitar o ASC para IoT em seu Hub IoT, o Azure Security Center para insights de IoT é exibido como o recurso **de segurança** no Azure IoT Hub e como **IoT** no Azure Security Center.

## <a name="supported-service-regions"></a>Regiões de serviço com suporte

O Azure Security Center for IoT é atualmente suportado para Hubs IoT nas seguintes regiões do Azure:

- Centro dos EUA
- Leste dos EUA
- Leste dos EUA 2
- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2
- Centro-Sul dos EUA
- Centro-Norte dos EUA
- Canadá Central
- Leste do Canadá
- Norte da Europa
- Sul do Brasil
- França Central
- Oeste do Reino Unido
- Sul do Reino Unido
- Europa Ocidental
- Norte da Europa
- Oeste do Japão
- Leste do Japão
- Sudeste da Austrália
- Leste da Austrália
- Leste da Ásia
- Sudeste Asiático
- Coreia Central
- Sul da Coreia
- Índia Central
- Sul da Índia

O Azure Security Center for IoT encaminha todo o tráfego de todas as regiões europeias para o data center regional da Europa Ocidental e todas as regiões restantes para o data center regional dos EUA central.

## <a name="wheres-my-iot-hub"></a>Onde está meu Hub ioT?

Verifique a localização do IoT Hub para verificar a disponibilidade do serviço antes de começar.

1. Abra seu Hub IoT.
1. Clique em **Visão Geral**.
1. Verifique se o local listado corresponde a uma das [regiões de serviço suportadas.](#supported-service-regions)

## <a name="supported-platforms-for-agents"></a>Plataformas suportadas para agentes

O Azure Security Center para agentes de IoT suporta uma lista crescente de dispositivos e plataformas. Consulte a [lista de plataformas suportadas](how-to-deploy-agent.md) para verificar sua biblioteca de dispositivos existente ou planejada.

## <a name="next-steps"></a>Próximas etapas

- Leia a visão geral da segurança do Azure [IoT](overview.md)
- Saiba como [ativar o serviço](quickstart-onboard-iot-hub.md)
- Leia o [Centro de Segurança do Azure para Perguntas Frequentes sobre IoT](resources-frequently-asked-questions.md)
- Saiba como [entender o Azure Security Center para alertas de IoT](concept-security-alerts.md)
