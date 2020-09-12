---
title: Módulo de segurança para visão geral dos RTOS do Azure
description: Saiba mais sobre o módulo de segurança para suporte e implementação dos RTOS do Azure como parte da central de segurança do Azure para o serviço de IoT.
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: bcf98f503b0f0c8bf76435d9884297d5f53d65e6
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514468"
---
# <a name="overview-security-module-for-azure-rtos-preview"></a>Visão geral: módulo de segurança para RTOS do Azure (versão prévia)

O módulo de segurança da central de segurança do Azure para IoT RTOS fornece uma solução de segurança abrangente para dispositivos do Azure RTOS. Os RTOS do Azure agora são fornecidos com o módulo de segurança de IoT do Azure interno e fornecem cobertura para ameaças comuns e possíveis atividades mal-intencionadas em dispositivos do sistema operacional em tempo real. 

![Central de segurança do Azure para RTOS do Azure para IoT](./media/architecture/azure-rtos-security-monitoring.png)


O módulo de segurança para os RTOS do Azure oferece os seguintes recursos: 
- Detecção de atividade de rede mal-intencionada
- Baseado em alerta personalizado, comportamento do dispositivo linha de base
- Melhorar a higiene de segurança do dispositivo

### <a name="detection-of-malicious-network-activities"></a>Detecção de atividades de rede maliciosas

A atividade de rede de entrada e saída de cada dispositivo é monitorada (protocolos com suporte: TCP, UDP, ICMP em IPv4 e IPv6). A central de segurança do Azure para IoT inspeciona cada uma dessas atividades de rede no feed do Microsoft Threat Intelligence. O feed é atualizado em tempo real com milhões de indicadores de ameaça exclusivos coletados em todo o mundo. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>A linha de base do comportamento do dispositivo baseado em alertas personalizados

A linha de base permite o clustering de dispositivos em grupos de segurança e a definição do comportamento esperado de cada grupo. Como os dispositivos IoT normalmente são projetados para operar em cenários bem definidos e limitados, é fácil criar uma linha de base que defina o comportamento esperado usando um conjunto de parâmetros. Qualquer desvio da linha de base dispara um alerta. 

### <a name="improve-your-device-security-hygiene"></a>Melhorar a higiene de segurança do dispositivo

Aproveitando a infraestrutura recomendada que a central de segurança do Azure para IoT fornece, obter conhecimento e informações sobre problemas em seu ambiente que afetam e danificam a postura de segurança de seus dispositivos. A postura de segurança de dispositivo IoT fraca pode permitir que possíveis ataques sejam bem-sucedidos se deixados inalterados, pois a segurança sempre é medida pelo link mais fraco em qualquer organização. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Introdução à proteção de dispositivos do Azure RTOS

O módulo de segurança para os RTOS do Azure é fornecido como um download gratuito para seus dispositivos. A central de segurança do Azure para o serviço de nuvem do IoT está disponível com uma avaliação de 30 dias por assinatura do Azure. Baixe o [módulo de segurança para os RTOs do Azure](https://github.com/azure-rtos/iot-security-module-preview) para começar. 


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o módulo de segurança para o serviço de RTOS do Azure. Para saber mais sobre o módulo de segurança e começar, consulte os seguintes artigos:

- [Conceitos do módulo de segurança de IoT dos RTOS do Azure](concept-rtos-security-module.md)
- [Início rápido: módulo de segurança de IoT dos RTOS do Azure](quickstart-azure-rtos-security-module.md)


