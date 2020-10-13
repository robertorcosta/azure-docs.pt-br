---
title: Azure Defender para IoT
description: Saiba mais sobre o Azure Defender para IoT
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2f41aac085fef62e0356a93a07823f21d7ba3667
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448334"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Introdução ao Azure Defender para IoT

Unifique o gerenciamento de segurança e habilite a detecção e a análise de ameaças de ponta a ponta nas cargas de trabalho de nuvem híbrida e na sua solução de IoT do Azure.

O Azure Defender para IoT fornece:

- **Descoberta de ativos e mapeamento de rede**, incluindo detalhes como fabricante do dispositivo, tipo de dispositivo e modo de comunicação dos dispositivos na rede
- **Gerenciamento de vulnerabilidades**, incluindo informações sobre CVEs, portas abertas e conexões não autorizadas com a Internet
- **Monitoramento contínuo de ameaças**, com alertas em tempo real indicando qualquer atividade anormal ou não autorizada, como ataques direcionados ou malware

Os detalhes completos estão disponíveis na [documentação dedicada](https://docs.microsoft.com/azure/asc-for-iot/overview).

## <a name="availability"></a>Disponibilidade
|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (em disponibilidade geral)|
|Preço:|Exige o [Azure Defender](security-center-pricing.md)|
|Funções e permissões necessárias:|Permissões de gravação nos NSGs do computador|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Quais dispositivos o Azure Defender para IoT pode proteger?
Ao combinar as funcionalidades da Central de Segurança, do Azure Defender e da tecnologia sem agente do CyberX, você pode proteger:

- **Dispositivos Greenfield** conectados por meio do Hub IoT. Isso permite que as organizações acelerem as iniciativas de IoT protegendo dispositivos modernos, nativos da Internet e dispositivos ICS/SCADA tradicionais com uma só solução unificada.
    - Integre novos dispositivos e aplique políticas de segurança nas suas cargas de trabalho (dispositivos folha, dispositivos Microsoft Edge, Hub IoT) para garantir a conformidade com os padrões de segurança e a postura de segurança aprimorada.

- Os **dispositivos Brownfield não gerenciados** usados em ambientes de OT (Tecnologia Operacional), como manufatura, BMS (sistemas de gerenciamento predial), ciências naturais, serviços de energia e água, petróleo e gás e logística. 
    - Para proteger dispositivos não gerenciados, o Azure Defender para IoT usa um sensor local para NTA (análise de tráfego de rede) passiva e não invasiva que tenha impacto de desempenho zero em ambientes de OT. 
    - Ele também incorpora uma compreensão aprofundada de protocolos de IoT/OT especializados, combinados com a análise comportamental com reconhecimento de IoT/OT patenteada e o machine learning, para eliminar a necessidade de configurar qualquer regra ou assinatura, resultando em implantações típicas em minutos ou horas, em vez de dias ou semanas. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Integração do Azure Defender para IoT ao Azure Sentinel
Para habilitar a governança e o monitoramento de segurança de TI/OT unificada, o Azure Defender para IoT integra-se nativamente ao [Azure Sentinel](../sentinel/overview.md).

As equipes de SecOps podem:

- Detectar ameaças de IoT/OT e responder rapidamente a elas por meio de guias estratégicos de SOAR específicos de OT incluídos no Sentinel
- Beneficiar-se da inteligência contra ameaças específica de IoT/OT continuamente atualizada, fornecida pela Section 52, a equipe interna de inteligência contra ameaças de IoT/OT da Microsoft
- Integrar o Azure Defender para IoT a fluxos de trabalho de SOC existentes e ferramentas de segurança de terceiros, como o Splunk, o IBM QRadar e o ServiceNow


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu mais sobre o Azure Defender para IoT na Central de Segurança do Azure. Para obter mais informações, consulte:

- [Apresentação da Central de Segurança do Azure para IoT](../asc-for-iot/overview.md)
