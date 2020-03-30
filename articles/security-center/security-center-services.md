---
title: Recursos suportados disponíveis no Azure Security Center | Microsoft Docs
description: Este documento fornece uma lista de serviços suportados pelo Azure Security Center.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245233"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de recursos para máquinas

As tabelas abaixo mostram os recursos do Azure Security Center que estão disponíveis para máquinas virtuais e servidores.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Recursos suportados para máquinas virtuais e servidores<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Máquinas do Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Recurso**|**Máquinas virtuais do Azure**|**Conjuntos de dimensionamento de máquina virtual do Azure**|**Máquinas não-azure**|**Preços**
|[Integração atp do Microsoft Defender](security-center-wdatp.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Standard|
|[Análise comportamental da máquina virtual (e alertas de segurança)](threat-protection.md)|✔|✔|✔|Recomendações (Grátis) </br></br> Alertas de segurança (Padrão)|
|[Alertas de segurança sem arquivos](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Alertas de segurança baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso ao VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação de vulnerabilidade nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitoramento de integridade de arquivos](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controles adaptativos de aplicativos](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Endurecimento de rede adaptativa](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controles de rede adaptável|✔|✔|-|Standard|
|[Relatórios de & de & de conformidade normativa](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Recomendações e proteção contra ameaças em contêineres IaaS hospedados pelo Docker|-|-|-|Standard|
|Avaliação de patches do Sistema Operacional ausente|✔|✔|✔|Grátis|
|Avaliação de configurações erradas de segurança|✔|✔|✔|Grátis|
|[Avaliação de proteção de endpoint](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Grátis|
|Avaliação de criptografia de disco|✔|✔|-|Grátis|
|Avaliação de vulnerabilidades de terceiros|✔|-|-|Grátis|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Grátis|


### <a name="linux-machines"></a>[Máquinas do Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Recurso**|**Máquinas virtuais do Azure**|**Conjuntos de dimensionamento de máquina virtual do Azure**|**Máquinas não-azure**|**Preços**
|[Integração atp do Microsoft Defender](security-center-wdatp.md)|-|-|-|Standard|
|[Análise comportamental da máquina virtual (e alertas de segurança)](security-center-alerts-iaas.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Recomendações (Grátis) </br></br> Alertas de segurança (Padrão)|
|[Alertas de segurança sem arquivos](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Alertas de segurança baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso ao VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação de vulnerabilidade nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitoramento de integridade de arquivos](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controles adaptativos de aplicativos](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Endurecimento de rede adaptativa](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controles de rede adaptável|✔|✔|-|Standard|
|[Relatórios de & de & de conformidade normativa](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Recomendações e proteção contra ameaças em contêineres IaaS hospedados pelo Docker|✔|✔|✔|Standard|
|Avaliação de patches do Sistema Operacional ausente|✔|✔|✔|Grátis|
|Avaliação de configurações erradas de segurança|✔|✔|✔|Grátis|
|[Avaliação de proteção de endpoint](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Grátis|
|Avaliação de criptografia de disco|✔|✔|-|Grátis|
|Avaliação de vulnerabilidades de terceiros|✔|-|-|Grátis|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Grátis|

--- 


> [!TIP]
>Para experimentar recursos que estão disponíveis apenas no nível de preços padrão, os usuários de nível gratuito podem se inscrever em um teste de 30 dias. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de ponto final suportadas<a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se você pode usar a Central de Segurança do Azure para instalar cada solução para você.
 - Quais soluções de proteção de ponto de extremidade a Central de Segurança pode descobrir. Se uma solução de proteção de ponto final desta lista for descoberta, o Security Center não recomendará a instalação de uma.

Para obter informações sobre quando as recomendações são geradas para cada uma dessas proteções, consulte [Avaliação e Recomendações de Proteção de Endpoint](security-center-endpoint-protection.md).

| Proteção do ponto de extremidade| Plataformas | Instalação da Central de Segurança | Descoberta da Central de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, Integrado no SO| Sim |
| System Center Endpoint Protection (antimalware da Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (consulte a observação abaixo) | Via extensão | Sim |
| Trend Micro – Todas as versões* | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família do Servidor Linux  | Não | Sim**\*** |
| Sophos V9+| Família do Servidor Linux  | Não | Sim**\***  |

 **\*** O estado de cobertura e os dados de suporte estão atualmente disponíveis apenas no espaço de trabalho do Log Analytics associado às suas assinaturas protegidas. Não está refletido no portal do Azure Security Center.

> [!NOTE]
> - A detecção do System Center Endpoint Protection (SCEP) em uma máquina virtual do Windows Server 2008 R2 exige que o SCEP seja instalado após o PowerShell 3.0 (ou uma versão superior).
> - A detecção da proteção Trend Micro é suportada para agentes de Segurança Profunda.  Os agentes do OfficeScan não são suportados.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [o Security Center coleta dados e o Log Analytics Agent](security-center-enable-data-collection.md).
- Saiba como [o Security Center gerencia e protege dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar o Azure Security Center](security-center-planning-and-operations-guide.md).
- Revise as [plataformas que suportam o centro de segurança.](security-center-os-coverage.md)
- Saiba mais sobre [proteção contra ameaças para máquinas Windows e Linux no Azure Security Center](threat-protection.md#windows-machines).
- Encontre [perguntas frequentes sobre o Azure Security Center](faq-general.md).