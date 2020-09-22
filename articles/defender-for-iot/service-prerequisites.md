---
title: Componentes & pré-requisitos
description: Detalhes de tudo o que é necessário para começar a usar os pré-requisitos de serviço do Azure defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19263f8db58c8d20288d3ae74c24efd85667bc33
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90932966"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Pré-requisitos do Azure defender para IoT

Este artigo fornece uma explicação dos diferentes componentes do serviço defender para IoT, o que você precisa começar e explica os conceitos básicos para ajudar a entender o serviço.

## <a name="minimum-requirements"></a>Requisitos mínimos

- Camada padrão do Hub IoT
  - Privilégios de nível do **proprietário** da função do Azure
- [Espaço de trabalho Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Central de segurança do Azure (recomendado)
  - O uso da central de segurança do Azure é uma recomendação, e não um requisito. Sem a central de segurança do Azure, você não poderá exibir os outros recursos do Azure no Hub IoT.

## <a name="working-with-defender-for-iot-service"></a>Trabalhando com o serviço defender para IoT

Os insights e relatórios do defender for IoT estão disponíveis usando o Hub IoT do Azure e a central de segurança do Azure. Para habilitar o defender para IoT no Hub IoT do Azure, é necessária uma conta com privilégios de nível de **proprietário** . Depois de habilitar o ASC para IoT em seu hub IoT, o defender for IoT insights será exibido como o recurso de **segurança** no Hub IOT do Azure e como  **IOT** na central de segurança do Azure.

## <a name="supported-service-regions"></a>Regiões de serviço com suporte

No momento, o defender para IoT tem suporte para hubs IoT nas seguintes regiões do Azure:

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

O defender para IoT roteia todo o tráfego de todas as regiões européias para a Europa Ocidental data center regionais e todas as regiões restantes para o data center regional EUA Central.

## <a name="wheres-my-iot-hub"></a>Onde está meu Hub IoT?

Verifique o local do Hub IoT para verificar a disponibilidade do serviço antes de começar.

1. Abra seu Hub IoT.
1. Clique em **Visão Geral**.
1. Verifique se o local listado corresponde a uma das [regiões de serviço com suporte](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Plataformas com suporte para agentes

Agentes do defender for IoT dão suporte a uma lista crescente de dispositivos e plataformas. Consulte a [lista de plataformas com suporte](how-to-deploy-agent.md) para verificar sua biblioteca de dispositivos existente ou planejada.

## <a name="next-steps"></a>Próximas etapas

- Leia a [visão geral](overview.md) da segurança de IOT do Azure
- Saiba como [habilitar o serviço](quickstart-onboard-iot-hub.md)
- Leia as [perguntas frequentes do defender for IOT](resources-frequently-asked-questions.md)
- Explore como [entender o defender para alertas de IOT](concept-security-alerts.md)
