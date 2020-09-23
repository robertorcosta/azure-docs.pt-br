---
title: Módulo de segurança para visão geral dos RTOS do Azure
description: Saiba mais sobre o módulo de segurança para suporte e implementação dos RTOS do Azure como parte do serviço defender para IoT
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 0cfd1e0ce16008c6f7fd128d561ad361bcc53b87
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933276"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Visão geral: módulo de segurança do defender para IoT para RTOS do Azure (versão prévia)

O módulo de segurança do defender for IoT para RTOS fornece uma solução de segurança abrangente para dispositivos do Azure RTOS. Os RTOS do Azure agora são fornecidos com o módulo de segurança de IoT do Azure interno e fornecem cobertura para ameaças comuns e possíveis atividades mal-intencionadas em dispositivos do sistema operacional em tempo real. 

![RTOS para Azure para IoT](./media/architecture/azure-rtos-security-monitoring.png)


O módulo de segurança para os RTOS do Azure oferece os seguintes recursos: 
- Detecção de atividade de rede mal-intencionada
- Baseado em alerta personalizado, comportamento do dispositivo linha de base
- Melhorar a higiene de segurança do dispositivo

## <a name="detection-of-malicious-network-activities"></a>Detecção de atividades de rede maliciosas

A atividade de rede de entrada e saída de cada dispositivo é monitorada (protocolos com suporte: TCP, UDP, ICMP em IPv4 e IPv6). O defender para IoT inspeciona cada uma dessas atividades de rede no feed do Microsoft Threat Intelligence. O feed é atualizado em tempo real com milhões de indicadores de ameaça exclusivos coletados em todo o mundo. 

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>A linha de base do comportamento do dispositivo baseado em alertas personalizados

A linha de base permite o clustering de dispositivos em grupos de segurança e a definição do comportamento esperado de cada grupo. Como os dispositivos IoT normalmente são projetados para operar em cenários bem definidos e limitados, é fácil criar uma linha de base que defina o comportamento esperado usando um conjunto de parâmetros. Qualquer desvio da linha de base dispara um alerta. 

## <a name="improve-your-device-security-hygiene"></a>Melhorar a higiene de segurança do dispositivo

Ao aproveitar a infraestrutura recomendada do defender para IoT, você obterá conhecimento e informações sobre problemas em seu ambiente que afetam e danificam a postura de segurança de seus dispositivos. A postura de segurança de dispositivo IoT fraca pode permitir que possíveis ataques sejam bem-sucedidos se deixados inalterados, pois a segurança sempre é medida pelo link mais fraco em qualquer organização. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Introdução à proteção de dispositivos do Azure RTOS

O módulo de segurança para os RTOS do Azure é fornecido como um download gratuito para seus dispositivos. O serviço de nuvem do defender para IoT está disponível com uma avaliação de 30 dias por assinatura do Azure. Baixe o [módulo de segurança para os RTOs do Azure](https://github.com/azure-rtos/iot-security-module-preview) para começar. 


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o módulo de segurança para o serviço de RTOS do Azure. Para saber mais sobre o módulo de segurança e começar, consulte os seguintes artigos:

- [Conceitos do módulo de segurança de IoT dos RTOS do Azure](concept-rtos-security-module.md)
- [Início rápido: módulo de segurança de IoT dos RTOS do Azure](quickstart-azure-rtos-security-module.md)
