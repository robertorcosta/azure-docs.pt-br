---
title: Recursos com suporte disponíveis na central de segurança do Azure | Microsoft Docs
description: Este documento fornece uma lista de serviços com suporte pela central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79245233"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de recursos para computadores

As tabelas a seguir mostram os recursos da central de segurança do Azure que estão disponíveis para máquinas virtuais e servidores.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Recursos com suporte para máquinas virtuais e servidores<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Máquinas do Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Recurso**|**Máquinas Virtuais do Azure**|**Conjuntos de dimensionamento de máquina virtual do Azure**|**Computadores não Azure**|**Preços**
|[Integração do Microsoft defender ATP](security-center-wdatp.md)|✔</br>(em versões com suporte)|✔</br>(em versões com suporte)|✔|Standard|
|[Análise comportamental de máquina virtual (e alertas de segurança)](threat-protection.md)|✔|✔|✔|Recomendações (gratuito) </br></br> Alertas de segurança (Standard)|
|[Alertas de segurança de arquivo](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Alertas de segurança baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso à VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação de vulnerabilidades nativas](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitoramento de integridade de arquivo](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controles de aplicativo adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Proteção de rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controles de rede adaptável|✔|✔|-|Standard|
|[Painel de conformidade regulatória & relatórios](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Recomendações e proteção contra ameaças em contêineres de IaaS hospedados pelo Docker|-|-|-|Standard|
|Avaliação de patches do sistema operacional ausente|✔|✔|✔|Grátis|
|Avaliação de incorretas de segurança|✔|✔|✔|Grátis|
|[Avaliação do Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Grátis|
|Avaliação de criptografia de disco|✔|✔|-|Grátis|
|Avaliação de vulnerabilidade de terceiros|✔|-|-|Grátis|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Grátis|


### <a name="linux-machines"></a>[Máquinas do Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Recurso**|**Máquinas Virtuais do Azure**|**Conjuntos de dimensionamento de máquina virtual do Azure**|**Computadores não Azure**|**Preços**
|[Integração do Microsoft defender ATP](security-center-wdatp.md)|-|-|-|Standard|
|[Análise comportamental de máquina virtual (e alertas de segurança)](security-center-alerts-iaas.md)|✔</br>(em versões com suporte)|✔</br>(em versões com suporte)|✔|Recomendações (gratuito) </br></br> Alertas de segurança (Standard)|
|[Alertas de segurança de arquivo](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Alertas de segurança baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso à VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação de vulnerabilidades nativas](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitoramento de integridade de arquivo](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controles de aplicativo adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Proteção de rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controles de rede adaptável|✔|✔|-|Standard|
|[Painel de conformidade regulatória & relatórios](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Recomendações e proteção contra ameaças em contêineres de IaaS hospedados pelo Docker|✔|✔|✔|Standard|
|Avaliação de patches do sistema operacional ausente|✔|✔|✔|Grátis|
|Avaliação de incorretas de segurança|✔|✔|✔|Grátis|
|[Avaliação do Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Grátis|
|Avaliação de criptografia de disco|✔|✔|-|Grátis|
|Avaliação de vulnerabilidade de terceiros|✔|-|-|Grátis|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Grátis|

--- 


> [!TIP]
>Para experimentar os recursos que estão disponíveis apenas no tipo de preço Standard, os usuários da camada gratuita podem se registrar em uma avaliação de 30 dias. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de ponto de extremidade com suporte<a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se você pode usar a Central de Segurança do Azure para instalar cada solução para você.
 - Quais soluções de proteção de ponto de extremidade a Central de Segurança pode descobrir. Se uma solução de proteção de ponto de extremidade dessa lista for descoberta, a central de segurança não recomendará a instalação de uma.

Para obter informações sobre quando as recomendações são geradas para cada uma dessas proteções, consulte [Endpoint Protection avaliação e recomendações](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação da Central de Segurança | Descoberta da Central de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, Integrado no SO| Sim |
| System Center Endpoint Protection (antimalware da Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (consulte a observação abaixo) | Via extensão | Sim |
| Trend Micro – todas as versões * | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família de servidores Linux  | Não | Ok**\*** |
| Sophos v9 +| Família de servidores Linux  | Não | Ok**\***  |

 **\*** O estado de cobertura e os dados de suporte estão atualmente disponíveis apenas no espaço de trabalho Log Analytics associado às suas assinaturas protegidas. Ele não é refletido no portal da central de segurança do Azure.

> [!NOTE]
> - A detecção do System Center Endpoint Protection (SCEP) em uma máquina virtual do Windows Server 2008 R2 exige que o SCEP seja instalado após o PowerShell 3.0 (ou uma versão superior).
> - Há suporte para a detecção da proteção Trend Micro para agentes de segurança profundas.  Não há suporte para agentes do OfficeScan.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [a central de segurança coleta dados e o agente de log Analytics](security-center-enable-data-collection.md).
- Saiba como a [central de segurança gerencia e protege os dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar a central de segurança do Azure](security-center-planning-and-operations-guide.md).
- Examine as [plataformas que dão suporte à central de segurança](security-center-os-coverage.md).
- Saiba mais sobre [a proteção contra ameaças para computadores Windows e Linux na central de segurança do Azure](threat-protection.md#windows-machines).
- Encontre [perguntas frequentes sobre a central de segurança do Azure](faq-general.md).