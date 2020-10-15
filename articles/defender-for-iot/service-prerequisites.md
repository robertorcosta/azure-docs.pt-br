---
title: Componentes & pré-requisitos
description: Detalhes de tudo o que é necessário para começar a usar os pré-requisitos de serviço do Azure defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089172"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Pré-requisitos do Azure defender para IoT

Este artigo fornece uma explicação dos diferentes componentes do serviço defender para IoT, o que você precisa começar e explica os conceitos básicos para ajudar a entender o serviço.

## <a name="minimum-requirements"></a>Requisitos mínimos

- Monitoramento sem agente para dispositivos IoT e OT (com base na tecnologia CyberX)
    - Comutadores de rede com suporte para monitoramento de tráfego por meio de porta SPAN
    - Dispositivos de hardware para sensor NTA, para obter mais informações, consulte [hardware certificado](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)
    - Função **colaborador** de assinatura do Azure (necessária somente durante a integração para definir dispositivos confirmados)
    - Função de **colaborador** do Hub IOT (camada gratuita ou Standard) (para gerenciamento conectado à nuvem)
- Segurança para dispositivos IoT gerenciados gerenciados por meio do Hub IoT do Azure
    - Função de **colaborador** do Hub IOT (camada Standard)
    - Hub IoT: **a alternância de recursos do Azure defender para IOT** deve ser habilitada
    - Para o suporte ao módulo de segurança no nível do dispositivo  
        - Agentes do defender for IoT dão suporte a uma lista crescente de dispositivos e plataformas, consulte a [lista de plataformas com suporte](how-to-deploy-agent.md)


## <a name="supported-service-regions"></a>Regiões de serviço com suporte

Consulte [regiões com suporte do Hub IOT](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) para obter mais informações. 

O defender para IoT roteia todo o tráfego de todas as regiões européias para a Europa Ocidental data center regionais e todas as regiões restantes para o data center regional EUA Central.

## <a name="next-steps"></a>Próximas etapas

- Leia a [visão geral](overview.md) da segurança de IOT do Azure
- Saiba como [habilitar o serviço](quickstart-onboard-iot-hub.md)
- Leia as [perguntas frequentes do defender for IOT](resources-frequently-asked-questions.md)
- Explore como [entender o defender para alertas de IOT](concept-security-alerts.md)
