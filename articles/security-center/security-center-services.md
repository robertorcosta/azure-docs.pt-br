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
ms.openlocfilehash: d756f9dfbd0012f884bb0c4a1e27efc76d613234
ms.sourcegitcommit: 387da88b8262368c1b67fffea58fe881308db1c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71982834"
---
# <a name="supported-features-available-in-azure-security-center"></a>Recursos com suporte disponíveis na central de segurança do Azure

> [!NOTE]
>Alguns recursos estão disponíveis apenas com a camada Standard. Se você ainda não tiver se inscrito na camada Standard da central de segurança, um período de avaliação gratuita estará disponível. Consulte a [página de preços da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/) para obter mais informações.

As seções a seguir mostram os recursos da central de segurança que estão disponíveis para suas [plataformas com suporte](security-center-os-coverage.md).

* [Máquinas virtuais/servidores](#vm-server-features)
* [Serviços de PaaS](#paas-services)


## Recursos com suporte para máquina virtual/servidor<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Servidor|Windows|||Linux|||Preços|
|----|----|----|----|----|----|----|----|
|**Ambiente**|**As tabelas**||**Não Azure**|**As tabelas**||**Não Azure**||
||**Máquina Virtual**|**Conjunto de Dimensionamento de Máquinas Virtuais**||**Máquina Virtual**|**Conjunto de Dimensionamento de Máquinas Virtuais**|
|Alertas de detecção de ameaças de análise comportamental de máquina virtual|✔|✔|✔|✔ (em versões compatíveis)|✔ (em versões compatíveis)|✔|Recomendações (gratuito) detecção de ameaças (padrão)|
|Alertas de detecção de ameaças baseadas em rede|✔|✔|X|✔|✔|X|Standard|
|Integração do Microsoft defender ATP|✔ (em versões compatíveis)|✔ (em versões compatíveis)|✔|X|X|X|Standard|
|Patches ausentes|✔|✔|✔|✔|✔|✔|Gratuito|
|Configurações de segurança|✔|✔|✔|✔|✔|✔|Gratuito|
|Avaliação do Endpoint Protection|✔|✔|✔|X|X|X|Gratuito|
|Acesso à VM just-in-time|✔|X|X|✔|X|X|Standard|
|Controles de aplicativo adaptáveis|✔|X|✔|✔|X|✔|Standard|
|Monitoramento de Integridade do Arquivo|✔|✔|✔|✔|✔|✔|Standard|
|Avaliação de criptografia de disco|✔|✔|X|✔|✔|X|Gratuito|
|Implantação de terceiros|✔|X|X|✔|X|X|Gratuito|
|Avaliação do NSG|✔|✔|X|✔|✔|X|Gratuito|
|Detecção de ameaças sem arquivo|✔|✔|✔|X|X|X|Standard|
|Mapa de rede|✔|✔|X|✔|✔|X|Standard|
|Proteção de Rede Adaptável|✔|X|X|✔|X|X|Standard|
|Controles de rede adaptável|✔|✔|X|✔|✔|X|Standard|
|Painel de conformidade regulatória & relatórios|✔|✔|✔|✔|✔|✔|Standard|
|Recomendações e detecção de ameaças em contêineres de IaaS hospedados pelo Docker|X|X|X|✔|✔|✔|Standard|

### Soluções de proteção de ponto de extremidade com suporte<a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se você pode usar a Central de Segurança do Azure para instalar cada solução para você.
 - Quais soluções de proteção de ponto de extremidade a Central de Segurança pode descobrir. Se uma dessas soluções de proteção de ponto de extremidade for descoberta, a Central de Segurança não recomendará a instalação de uma.

Para obter informações sobre quando as recomendações são geradas para cada uma dessas proteções, consulte [Endpoint Protection avaliação e recomendações](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação da Central de Segurança | Descoberta da Central de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, Integrado no SO| Sim |
| System Center Endpoint Protection (antimalware da Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (consulte a observação abaixo) | Via extensão | Sim |
| Trend Micro – todas as versões * | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família de servidores Linux  | Não | Sim **\*** |
| Sophos v9 +| Família de servidores Linux  | Não | Sim **\***  |

 **\*** O estado de cobertura e os dados de suporte estão disponíveis no momento apenas no espaço de trabalho Log Analytics associado às suas assinaturas protegidas e não são refletidos no portal da central de segurança do Azure.

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
|Armazenamento de Blob|✔| ✔|
|Conta de armazenamento|✔| N/A|
|serviço de aplicativo|✔| ✔|
|Função|✔| X|
|Serviço de Nuvem|✔| X|
|VNET|✔| N/A|
|Subnet|✔| N/A|
|NIC|✔| N/A|
|NSG|✔| N/A|
|Assinatura|✔ **| ✔|
|Conta do Lote|✔| X|
|Conta do Service Fabric|✔| X|
|Conta de automação|✔| X|
|Balanceador de carga|✔| X|
|Pesquisa|✔| X|
|Namespace de barramento de serviço|✔| X|
|Stream Analytics|✔| X|
|Namespace de Hub de Eventos|✔| X|
|Aplicativos lógicos|✔| X|
|Redis|✔| N/A|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Cofre de chaves|✔| X|

\* Esses recursos são compatíveis atualmente com a versão prévia pública.

as recomendações \* @ no__t-1 Azure Active Directory (Azure AD) estão disponíveis somente para assinaturas padrão.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [a central de segurança coleta dados e o agente de log Analytics](security-center-enable-data-collection.md).
- Saiba como a [central de segurança gerencia e protege os dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar a Central de Segurança do Azure](security-center-planning-and-operations-guide.md).
- Examine as [plataformas que dão suporte à central de segurança](security-center-os-coverage.md).
- Saiba mais sobre [a detecção de ameaças para VMs & servidores na central de segurança do Azure](security-center-alerts-iaas.md).
- Encontre [perguntas frequentes sobre como usar a Central de Segurança do Azure](security-center-faq.md).
- Encontre [postagens no blog sobre a conformidade e segurança do Azure](https://blogs.msdn.com/b/azuresecurity/).
