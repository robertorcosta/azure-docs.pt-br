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
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: f5be0b43de8265b2b337c024117ae8f424e4a3bc
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403476"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de recursos para computadores

As duas guias abaixo mostram os recursos da central de segurança do Azure que estão disponíveis para servidores e máquinas virtuais Windows e Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Recursos com suporte para máquinas virtuais e servidores <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Máquinas do Windows**](#tab/features-windows)

|**Recurso**|**Máquinas Virtuais do Azure**|**Conjuntos de dimensionamento de máquina virtual do Azure**|**Computadores não Azure**|**Azure defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Integração do Microsoft defender ATP](security-center-wdatp.md)|✔</br>(em versões com suporte)|✔</br>(em versões com suporte)|✔|Sim|
|[Análise comportamental de máquina virtual (e alertas de segurança)](alerts-reference.md)|✔|✔|✔|Sim|
|[Alertas de segurança de arquivo](alerts-reference.md#alerts-windows)|✔|✔|✔|Sim|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Sim|
|[Acesso à VM Just-In-Time](security-center-just-in-time.md)|✔|-|-|Sim|
|[Avaliação de vulnerabilidades nativas](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Sim|
|[Monitoramento de integridade do arquivo](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sim|
|[Controles de aplicativo adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Sim|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Sim|
|[Proteção de rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Sim|
|[Painel de conformidade regulatória & relatórios](security-center-compliance-dashboard.md)|✔|✔|✔|Sim|
|Recomendações e proteção contra ameaças em contêineres de IaaS hospedados pelo Docker|-|-|-|Sim|
|Avaliação de patches do sistema operacional ausente|✔|✔|✔|Azure: não<br><br>Não Azure: Sim|
|Avaliação de incorretas de segurança|✔|✔|✔|Azure: não<br><br>Não Azure: Sim|
|[Avaliação do Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: não<br><br>Não Azure: Sim|
|Avaliação de criptografia de disco|✔</br>(para [cenários com suporte](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Não|
|Avaliação de vulnerabilidade de terceiros|✔|-|-|Não|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Não|


### <a name="linux-machines"></a>[**Máquinas do Linux**](#tab/features-linux)

|**Recurso**|**Máquinas Virtuais do Azure**|**Conjuntos de dimensionamento de máquina virtual do Azure**|**Computadores não Azure**|**Azure defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Integração do Microsoft defender ATP](security-center-wdatp.md)|-|-|-|Sim|
|[Análise comportamental de máquina virtual (e alertas de segurança)](security-center-alerts-iaas.md)|✔</br>(em versões com suporte)|✔</br>(em versões com suporte)|✔|Sim|
|[Alertas de segurança de arquivo](alerts-reference.md#alerts-windows)|-|-|-|Sim|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Sim|
|[Acesso à VM Just-In-Time](security-center-just-in-time.md)|✔|-|-|Sim|
|[Avaliação de vulnerabilidades nativas](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Sim|
|[Monitoramento de integridade do arquivo](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sim|
|[Controles de aplicativo adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Sim|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Sim|
|[Proteção de rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Sim|
|[Painel de conformidade regulatória & relatórios](security-center-compliance-dashboard.md)|✔|✔|✔|Sim|
|Recomendações e proteção contra ameaças em contêineres de IaaS hospedados pelo Docker|✔|✔|✔|Sim|
|Avaliação de patches do sistema operacional ausente|✔|✔|✔|Azure: não<br><br>Não Azure: Sim|
|Avaliação de incorretas de segurança|✔|✔|✔|Azure: não<br><br>Não Azure: Sim|
|[Avaliação do Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Não|
|Avaliação de criptografia de disco|✔</br>(para [cenários com suporte](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Não|
|Avaliação de vulnerabilidade de terceiros|✔|-|-|Não|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Não|

--- 


> [!TIP]
>Para experimentar os recursos que só estão disponíveis com o Azure defender, você pode registrar-se em uma avaliação de 30 dias. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de ponto de extremidade com suporte <a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se você pode usar a Central de Segurança do Azure para instalar cada solução para você.
 - Quais soluções de proteção de ponto de extremidade a Central de Segurança pode descobrir. Se uma solução de proteção de ponto de extremidade dessa lista for descoberta, a central de segurança não recomendará a instalação de uma.

Para obter informações sobre quando as recomendações são geradas para cada uma dessas proteções, consulte [Endpoint Protection avaliação e recomendações](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação da Central de Segurança | Descoberta da Central de Segurança |
|------|------|-----|-----|
| Microsoft Defender Antivírus| Windows Server 2016 ou posterior| Não, Integrado no SO| Sim |
| System Center Endpoint Protection (antimalware da Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (consulte a observação abaixo) | Via extensão | Sim |
| Trend Micro – segurança profunda | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família de servidores Linux  | Não | Ok **\*** |
| Sophos v9 +| Família de servidores Linux  | Não | Ok  **\***  |

 **\*** O estado de cobertura e os dados de suporte estão atualmente disponíveis apenas no espaço de trabalho Log Analytics associado às suas assinaturas protegidas. Ele não é refletido no portal da central de segurança do Azure.

> [!NOTE]
> A detecção do System Center Endpoint Protection (SCEP) em uma máquina virtual do Windows Server 2008 R2 requer que o SCEP seja instalado após o PowerShell (v 3.0 ou mais recente).


## <a name="next-steps"></a>Próximas etapas

- Saiba como [a central de segurança coleta dados e o agente de log Analytics](security-center-enable-data-collection.md).
- Saiba como a [central de segurança gerencia e protege os dados](security-center-data-security.md).
- Examine as [plataformas que dão suporte à central de segurança](security-center-os-coverage.md).