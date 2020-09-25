---
title: Azure Defender para IoT
description: Saiba mais sobre o Azure defender para IoT
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 307916680d9a05a5083aea057b2ef4b855a4ff57
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301798"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Introdução ao Azure defender para IoT

Unifique o gerenciamento de segurança e habilite a detecção e análise de ameaças de ponta a ponta em cargas de trabalho de nuvem híbrida e sua solução de IoT do Azure.

O Azure defender para IoT fornece:

- **Descoberta de ativos e mapeamento de rede**, incluindo detalhes como fabricante do dispositivo, tipo de dispositivo e como os dispositivos se comunicam na rede
- **Gerenciamento de vulnerabilidades**, incluindo informações sobre CVEs, portas abertas e conexões de Internet não autorizadas
- **Monitoramento contínuo de ameaças**, com alertas em tempo real que indicam qualquer atividade anômala ou não autorizada, como ataques direcionados ou malware

Os detalhes completos estão disponíveis na [documentação dedicada](https://docs.microsoft.com/azure/asc-for-iot/overview).

## <a name="availability"></a>Disponibilidade
|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (em disponibilidade geral)|
|Refere|Requer o [Azure defender](security-center-pricing.md)|
|Funções e permissões necessárias:|Permissões de gravação no NSGs da máquina|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) National/soberanas (US Gov, China gov, outros gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Quais dispositivos o Azure defender para IoT é seguro?
Ao combinar os recursos da central de segurança, do Azure defender e da tecnologia sem agente do CyberX, você pode proteger:

- **Dispositivos Greenfield** conectados via hub IOT. Isso permite que as organizações acelerem suas iniciativas de IoT protegendo dispositivos modernos, nativos da Internet e dispositivos ICS/SCADA tradicionais com uma única solução unificada.
    - Integre novos dispositivos e aplique políticas de segurança em suas cargas de trabalho (dispositivos folha, dispositivos Microsoft Edge, Hub IoT) para garantir a conformidade com os padrões de segurança e a postura de segurança aprimorada.

- **Dispositivos Brownfield não gerenciados** usados em ambientes de tecnologia operacional (OT), como manufatura, BMS (sistemas de gerenciamento de construção), ciências biomédicas, utilitários de energia e água, óleo & gás e logística. 
    - Para proteger dispositivos não gerenciados, o Azure defender para IoT usa um sensor local para análise passiva e não invasiva de tráfego de rede (NTA) que tem impacto de desempenho zero em ambientes de OT. 
    - Ele também incorpora uma compreensão aprofundada de protocolos de IoT/OT especializados, combinados com análise de comportamento de reconhecimento de IoT/OT patenteada e aprendizado de máquina, para eliminar a necessidade de configurar qualquer regra ou assinatura, resultando em implantações típicas em minutos ou horas, em vez de dias ou semanas. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Integração do Azure defender para IoT com o Azure Sentinel
Para habilitar o monitoramento e a governança de segurança e ti unificada, o Azure defender para IoT integra-se nativamente com o [Azure Sentinel](../sentinel/overview.md).

As equipes do SecOps podem:

- Detecte e responda rapidamente a ameaças de IoT/OT por meio de guias estratégicos de disparar específicos de OT incluídos com o sentinela
- Beneficie-se da inteligência de ameaças específica de IoT/OT, fornecida pela seção 52, da equipe de inteligência contra ameaças do IoT/OT interna da Microsoft
- Integre o Azure defender para IoT com fluxos de trabalho do SOC existentes e ferramentas de segurança de terceiros, como Splunk, IBM QRadar e ServiceNow


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure defender para IoT na central de segurança do Azure. Para obter mais informações, consulte:

- [Apresentação da Central de Segurança do Azure para IoT](../asc-for-iot/overview.md)
