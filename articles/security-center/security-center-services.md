---
title: Recursos com suporte disponíveis na Central de Segurança do Azure | Microsoft Docs
description: Este documento fornece uma lista dos serviços que têm suporte da Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 61e5c60317bf872b8e27a7bd2edea7247e01a3b8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91439424"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de recursos para computadores

As duas guias abaixo mostram os recursos da Central de Segurança do Azure disponíveis para servidores e máquinas virtuais do Windows e do Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Recursos com suporte para máquinas virtuais e servidores <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Máquinas do Windows**](#tab/features-windows)

|**Recurso**|**Máquinas Virtuais do Azure**|**Conjuntos de dimensionamento de máquina virtual do Azure**|**Computadores não Azure**|**Azure Defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Integração ao Microsoft Defender ATP](security-center-wdatp.md)|✔</br>(em versões compatíveis)|✔</br>(em versões compatíveis)|✔|Sim|
|[Análise comportamental de máquina virtual (e alertas de segurança)](alerts-reference.md)|✔|✔|✔|Sim|
|[Alertas de segurança sem arquivo](alerts-reference.md#alerts-windows)|✔|✔|✔|Sim|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Sim|
|[Acesso à VM Just-In-Time](security-center-just-in-time.md)|✔|-|-|Sim|
|[Avaliação de vulnerabilidades nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Sim|
|[Monitoramento de integridade do arquivo](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sim|
|[Controles de aplicativo adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Sim|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Sim|
|[Proteção de rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Sim|
|[Painel de conformidade regulatória e relatórios](security-center-compliance-dashboard.md)|✔|✔|✔|Sim|
|Recomendações e proteção contra ameaças em contêineres de IaaS hospedados pelo Docker|-|-|-|Sim|
|Avaliação de patches ausente do sistema operacional|✔|✔|✔|Azure: Não<br><br>Não Azure: Sim|
|Avaliação de configurações incorretas de segurança|✔|✔|✔|Azure: Não<br><br>Não Azure: Sim|
|[Avaliação da proteção de ponto de extremidade](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Não<br><br>Não Azure: Sim|
|Avaliação da criptografia de disco|✔</br>(para [cenários compatíveis](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Não|
|Avaliação de vulnerabilidades de terceiros|✔|-|-|Não|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Não|


### <a name="linux-machines"></a>[**Máquinas do Linux**](#tab/features-linux)

|**Recurso**|**Máquinas Virtuais do Azure**|**Conjuntos de dimensionamento de máquina virtual do Azure**|**Computadores não Azure**|**Azure Defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Integração ao Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|Sim|
|[Análise comportamental de máquina virtual (e alertas de segurança)](security-center-alerts-iaas.md)|✔</br>(em versões compatíveis)|✔</br>(em versões compatíveis)|✔|Sim|
|[Alertas de segurança sem arquivo](alerts-reference.md#alerts-windows)|-|-|-|Sim|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Sim|
|[Acesso à VM Just-In-Time](security-center-just-in-time.md)|✔|-|-|Sim|
|[Avaliação de vulnerabilidades nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Sim|
|[Monitoramento de integridade do arquivo](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sim|
|[Controles de aplicativo adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Sim|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Sim|
|[Proteção de rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Sim|
|[Painel de conformidade regulatória e relatórios](security-center-compliance-dashboard.md)|✔|✔|✔|Sim|
|Recomendações e proteção contra ameaças em contêineres de IaaS hospedados pelo Docker|✔|✔|✔|Sim|
|Avaliação de patches ausente do sistema operacional|✔|✔|✔|Azure: Não<br><br>Não Azure: Sim|
|Avaliação de configurações incorretas de segurança|✔|✔|✔|Azure: Não<br><br>Não Azure: Sim|
|[Avaliação da proteção de ponto de extremidade](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Não|
|Avaliação da criptografia de disco|✔</br>(para [cenários compatíveis](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Não|
|Avaliação de vulnerabilidades de terceiros|✔|-|-|Não|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Não|

--- 


> [!TIP]
>Para experimentar os recursos que só estão disponíveis no Azure Defender, inscreva-se em uma avaliação de 30 dias. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de ponto de extremidade com suporte <a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se você pode usar a Central de Segurança do Azure para instalar cada solução para você.
 - Quais soluções de proteção de ponto de extremidade a Central de Segurança pode descobrir. Se uma solução de proteção de ponto de extremidade dessa lista for descoberta, a Central de Segurança não recomendará a instalação de uma.

Para obter informações sobre quando as recomendações são geradas para cada uma dessas proteções, confira [Avaliação e recomendações de proteção de ponto de extremidade](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação da Central de Segurança | Descoberta da Central de Segurança |
|------|------|-----|-----|
| Microsoft Defender Antivírus| Windows Server 2016 ou posterior| Não, Integrado no SO| Sim |
| System Center Endpoint Protection (antimalware da Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (consulte a observação abaixo) | Via extensão | Sim |
| Trend Micro – Deep Security | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família de servidores Linux  | Não | Sim **\*** |
| Sophos V9 e posterior| Família de servidores Linux  | Não | Sim **\***  |

 **\*** O estado de cobertura e os dados de suporte estão atualmente disponíveis apenas no workspace do Log Analytics associado às assinaturas protegidas. Ele não é refletido no portal da Central de Segurança do Azure.

> [!NOTE]
> A detecção do SCEP (System Center Endpoint Protection) em uma máquina virtual do Windows Server 2008 R2 exige a instalação do SCEP após o PowerShell (v3.0 ou mais recente).


## <a name="next-steps"></a>Próximas etapas

- Saiba como a [Central de Segurança coleta os dados e o agente do Log Analytics](security-center-enable-data-collection.md).
- Saiba como a [Central de Segurança gerencia e protege os dados](security-center-data-security.md).
- Examine as [plataformas de que dão suporte à Central de Segurança](security-center-os-coverage.md).