---
title: Suporte a RTOS do Azure
description: Saiba mais sobre o suporte dos RTOS do Azure na central de segurança do Azure para o serviço de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095639"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>Central de segurança do Azure para solução de segurança de IoT para RTOS do Azure 

O módulo de segurança da central de segurança do Azure para IoT fornece uma solução de segurança abrangente para dispositivos do Azure RTOS. Os RTOS do Azure são fornecidos com um módulo de segurança interno que abrange ameaças comuns em dispositivos do sistema operacional em tempo real. 

![Central de segurança do Azure para RTOS do Azure para IoT](./media/architecture/azure-rtos-security-monitoring.png)


A central de segurança do Azure para o módulo de segurança de IoT com suporte aos RTOS do Azure oferece os seguintes recursos: 
- Detecção de atividade de rede mal-intencionada
- Baseado em alerta personalizado, comportamento do dispositivo linha de base
- Melhorar a higiene de segurança do dispositivo

### <a name="detection-of-malicious-network-activities"></a>Detecção de atividades de rede maliciosas

A atividade de rede de entrada e saída de cada dispositivo é monitorada (protocolos com suporte: TCP, UDP, ICMP em IPv4 e IPv6). A central de segurança do Azure para IoT inspeciona cada uma dessas atividades de rede no feed do Microsoft Threat Intelligence. O feed é atualizado em tempo real com milhões de indicadores de ameaça exclusivos coletados em todo o mundo. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>A linha de base do comportamento do dispositivo baseado em alertas personalizados

A linha de base permite o clustering de dispositivos em grupos de segurança e a definição do comportamento esperado de cada grupo. Como os dispositivos IoT normalmente são projetados para operar em cenários bem definidos e limitados, é fácil criar uma linha de base que defina o comportamento esperado usando um conjunto de parâmetros. Qualquer desvio da linha de base dispara um alerta. 

### <a name="improve-your-device-security-hygiene"></a>Melhorar a higiene de segurança do dispositivo

Aproveitando a infraestrutura recomendada que a central de segurança do Azure para IoT fornece, obter conhecimento e informações sobre problemas em seu ambiente que afetam e danificam a postura de segurança de seus dispositivos. A postura de segurança de dispositivo IoT deficiente pode permitir que possíveis ataques sejam bem-sucedidos se deixados inalterados, pois a segurança sempre é medida pelo link mais fraco em qualquer organização. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Introdução à proteção de dispositivos do Azure RTOS

- A central de segurança do Azure para o módulo de segurança de IoT para os RTOS do Azure é fornecida como um download gratuito para seus dispositivos. A central de segurança do Azure para o serviço de nuvem do IoT está disponível com uma avaliação de 30 dias por assinatura do Azure. Baixe a [central de segurança do Azure para o módulo de segurança de IOT para os RTOs do Azure](https://github.com/azure-rtos/iot-security-module-preview) para começar. 


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o suporte do Azure Security Center para IoT Azure RTOS. Para saber como começar e habilitar sua solução de segurança no Hub IoT, consulte os seguintes artigos:

- [Pré-requisitos do serviço](service-prerequisites.md)
- [Guia de Introdução](getting-started.md)
- [Configurar sua solução](quickstart-configure-your-solution.md)
- [Habilitar a segurança no Hub IoT](quickstart-onboard-iot-hub.md)
- [FAQ da central de segurança do Azure para IoT](resources-frequently-asked-questions.md)
- [Alertas de segurança da Central de Segurança do Azure para IoT](concept-security-alerts.md)
