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
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: b6de3a520150a95316371c4454fd2537f2beab5d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331137"
---
# <a name="supported-features-available-in-azure-security-center"></a>Recursos com suporte disponíveis na central de segurança do Azure

> [!NOTE]
>Alguns recursos estão disponíveis apenas com a camada Standard. Se você ainda não tiver se inscrito na camada Standard da central de segurança, um período de avaliação gratuita estará disponível. Para obter mais informações, consulte a [página de preços da central de segurança](https://azure.microsoft.com/pricing/details/security-center/).

As seções a seguir mostram os recursos da central de segurança que estão disponíveis para suas [plataformas com suporte](security-center-os-coverage.md).

* [Máquinas virtuais/servidores](#vm-server-features)
* [Serviços de PaaS](#paas-services)


## Recursos com suporte para máquina virtual/servidor<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Servidor|Windows|||Linux|||Preços|
|----|----|----|----|----|----|----|----|
|**Ambiente**|**Azure**||**Não Azure**|**Azure**||**Não Azure**||
||**Máquina Virtual**|**Conjunto de Dimensionamento de Máquinas Virtuais**||**Máquina Virtual**|**Conjunto de Dimensionamento de Máquinas Virtuais**|
|[Integração do Microsoft defender ATP](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (em versões compatíveis)|✔ (em versões compatíveis)|✔|X|X|X|Standard|
|[Alertas de detecção de ameaças de análise comportamental de máquina virtual](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (em versões compatíveis)|✔ (em versões compatíveis)|✔|Recomendações (gratuito) detecção de ameaças (padrão)|
|[Alertas de detecção de ameaças com arquivo](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|X|X|X|Standard|
|[Alertas de detecção de ameaças baseados em rede](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|X|✔|✔|X|Standard|
|[Acesso à VM just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|X|X|✔|X|X|Standard|
|[Monitoramento de integridade de arquivo](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Standard|
|[Controles de aplicativo adaptável](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|X|✔|✔|X|✔|Standard|
|[Mapa de rede](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|X|✔|✔|X|Standard|
|[Proteção de rede adaptável](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|X|X|✔|X|X|Standard|
|Controles de rede adaptável|✔|✔|X|✔|✔|X|Standard|
|[Painel de conformidade regulatória & relatórios](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Standard|
|Recomendações e detecção de ameaças em contêineres de IaaS hospedados pelo Docker|X|X|X|✔|✔|✔|Standard|
|Avaliação de patches do sistema operacional ausente|✔|✔|✔|✔|✔|✔|Gratuito|
|Avaliação de incorretas de segurança|✔|✔|✔|✔|✔|✔|Gratuito|
|[Avaliação do Endpoint Protection](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|X|X|X|Gratuito|
|Avaliação de criptografia de disco|✔|✔|X|✔|✔|X|Gratuito|
|Avaliação de vulnerabilidade de terceiros|✔|X|X|✔|X|X|Gratuito|
|[Avaliação de segurança de rede](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)|✔|✔|X|✔|✔|X|Gratuito|

### Soluções de proteção de ponto de extremidade com suporte<a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se você pode usar a Central de Segurança do Azure para instalar cada solução para você.
 - Quais soluções de proteção de ponto de extremidade a Central de Segurança pode descobrir. Se uma solução de proteção de ponto de extremidade dessa lista for descoberta, a central de segurança não recomendará a instalação de uma.

Para obter informações sobre quando as recomendações são geradas para cada uma dessas proteções, consulte [Endpoint Protection avaliação e recomendações](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação da Central de Segurança | Descoberta da Central de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, Integrado no SO| SIM |
| System Center Endpoint Protection (antimalware da Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (consulte a observação abaixo) | Via extensão | SIM |
| Trend Micro – todas as versões * | Família Windows Server  | Não | SIM |
| Symantec v12.1.1100+| Família Windows Server  | Não | SIM |
| McAfee v10+ | Família Windows Server  | Não | SIM |
| McAfee v10+ | Família de servidores Linux  | Não | Sim **\*** |
| Sophos v9 +| Família de servidores Linux  | Não | Sim **\***  |

 **\*** O estado de cobertura e os dados de suporte estão atualmente disponíveis apenas no espaço de trabalho Log Analytics associado às suas assinaturas protegidas. Ele não é refletido no portal da central de segurança do Azure.

> [!NOTE]
>
> - A detecção do System Center Endpoint Protection (SCEP) em uma máquina virtual do Windows Server 2008 R2 exige que o SCEP seja instalado após o PowerShell 3.0 (ou uma versão superior).
> - Há suporte para a detecção da proteção Trend Micro para agentes de segurança profundas.  Não há suporte para agentes do OfficeScan.


## Recursos <a name="paas-services"></a> com suporte de serviços de PaaS

Os recursos de PaaS a seguir têm suporte na central de segurança do Azure:

|Serviço|Recomendações (gratuito)|Detecção de ameaças (padrão)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB|X| ✔|
|Armazenamento de blob|✔| ✔|
|Conta de armazenamento|✔| ND|
|serviço de aplicativo|✔| ✔|
|Função|✔| X|
|Serviço de Nuvem|✔| X|
|VNET|✔| ND|
|Sub-rede|✔| ND|
|NIC|✔| ND|
|NSG|✔| ND|
|Subscription|✔ * *| ✔|
|Conta do Batch|✔| X|
|Conta do Service Fabric|✔| X|
|Conta de automação|✔| X|
|Load Balancer|✔| X|
|Pesquisa|✔| X|
|Namespace do Barramento de Serviço|✔| X|
|Stream Analytics|✔| X|
|Namespace do Hub de Eventos|✔| X|
|Aplicativos lógicos|✔| X|
|Redis|✔| ND|
|Análises Data Lake|✔| X|
|Repositório Data Lake|✔| X|
|Key Vault|✔| X|

\* Esses recursos são compatíveis atualmente com a versão prévia pública.

as recomendações \* @ no__t-1 Azure Active Directory (Azure AD) estão disponíveis somente para assinaturas padrão.

## <a name="next-steps"></a>Próximos passos

- Saiba como [a central de segurança coleta dados e o agente de log Analytics](security-center-enable-data-collection.md).
- Saiba como a [central de segurança gerencia e protege os dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar a Central de Segurança do Azure](security-center-planning-and-operations-guide.md).
- Examine as [plataformas que dão suporte à central de segurança](security-center-os-coverage.md).
- Saiba mais sobre [a detecção de ameaças para VMs & servidores na central de segurança do Azure](security-center-alerts-iaas.md).
- Encontre [perguntas frequentes sobre como usar a Central de Segurança do Azure](security-center-faq.md).
- Encontre [postagens no blog sobre a conformidade e segurança do Azure](https://blogs.msdn.com/b/azuresecurity/).
