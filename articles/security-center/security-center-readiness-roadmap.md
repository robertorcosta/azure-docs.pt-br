---
title: Roteiro de preparação da Central de Segurança do Azure | Microsoft Docs
description: Este documento fornece um roteiro de preparação para incrementar a Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: yurid
ms.openlocfilehash: 9d74ea2b967112a794cda204cbbfcac707e1d7c4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879455"
---
# <a name="azure-security-center-readiness-roadmap"></a>Roteiro de Preparação da Central de Segurança do Azure
Este documento fornece um roteiro de preparação que ajudará você a começar a usar a Central de Segurança do Azure.

## <a name="understanding-security-center"></a>Noções básicas sobre a Central de Segurança
A Central de Segurança do Azure fornece gerenciamento de segurança unificado e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, localmente e em outras nuvens. 

Use os seguintes recursos para começar com a Central de Segurança.

Artigos
* [Introdução à Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Guia de início rápido da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-get-started)

vídeos
* [Vídeo de introdução rápida](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [Visão geral dos recursos de resposta, de detecção e de prevenção da Central de Segurança](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planejamento e operações

Para tirar total proveito da Central de Segurança, é importante entender como as pessoas ou equipes diferentes em sua organização usam o serviço para atender às necessidades de operações seguras, monitoramento, administração resposta a incidentes.

Use os recursos a seguir para ajudá-lo durante os processos de planejamento e de operações.

Artigo
* [Guia de planejamento e operações da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)


### <a name="onboarding-computers-to-security-center"></a>Integração de computadores à Central de Segurança
A Central de Segurança detecta automaticamente as assinaturas ou workspaces do Azure não habilitados para a Central de Segurança Standard. Isso inclui assinaturas do Azure usando a Central de Segurança Gratuita e workspaces que não têm a solução da Segurança habilitada.

Use os recursos a seguir para ajudá-lo durante os processos de integração.

Artigo
* [Integração à Central de Segurança do Azure Standard para uma segurança aprimorada](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Vídeo
* [Central de Segurança do Azure híbrido - visão geral](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Como eliminar problemas de segurança usando a Central de Segurança
A Central de segurança coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros conectados, como firewall e soluções de proteção de ponto de extremidade, a fim de detectar ameaças reais e reduzir os falsos positivos.

Use os recursos a seguir para ajudá-lo a gerenciar alertas de segurança e a proteger seus recursos.

Artigos    
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Protegendo suas máquinas e aplicativos na Central de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Protegendo sua rede na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Proteção dos dados e do serviço SQL do Azure na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Vídeo    
* [Como eliminar problemas de segurança usando a Central de Segurança do Azure](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Central de Segurança para resposta a incidentes
Para reduzir custos e danos, é importante ter um plano de resposta a incidentes antes que um ataque ocorra. A Central de Segurança do Azure pode ser usada em diferentes estágios de uma resposta a incidentes.

Use os recursos a seguir para entender como a Central de Segurança pode ser incorporada ao processo de resposta a incidentes.

vídeos    
* [Central de Segurança do Azure em resposta a incidentes](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Responder rapidamente às ameaças com a operação de segurança de última geração e a investigação](https://youtu.be/e8iFCz5RM4g)

Artigos    
* [Uso da Central de Segurança do Azure para uma resposta a incidentes](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Automatize a resposta com a automação do fluxo de trabalho](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Defesa da nuvem avançada

As Máquinas Virtuais do Azure podem tirar proveito dos recursos avançados de defesa de nuvem na Central de Segurança. Esses recursos incluem acesso à máquina virtual (VM) just-in-time e controles de aplicativos adaptativos.

Use os seguintes recursos para saber como usar esses recursos na Central de Segurança.

vídeos    
* [Centro de Segurança Azure – Acesso Just-in-Time da VM](https://youtu.be/UOQb2FcdQnU)
* [Central de Segurança do Azure: Controles de Aplicativo Adaptáveis](https://youtu.be/wWWekI1Y9ck)

Artigos    
* [Gerenciar o acesso à máquina virtual usando JIT](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Controles de aplicativo adaptáveis na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Atividades práticas

* [Laboratório prático da Central de Segurança](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Guia estratégico de recomendação do WAF (Firewall do Aplicativo Web) na Central de Segurança](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Guia Estratégico da Central de Segurança do Azure: Alertas de Segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Recursos adicionais
* [Página de documentação da Central de Segurança](https://docs.microsoft.com/azure/security-center/)
* [Página de documentação da API REST da Central de Segurança](https://msdn.microsoft.com/library/mt704034.aspx)
* [Perguntas frequentes sobre a Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Página de preços da Central de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/)
* [Práticas recomendadas de segurança de identidade](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)
* [Práticas recomendadas de segurança da rede](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices)
* [Recomendações de PaaS](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Conformidade](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Os clientes de análise de log agora podem usar o Azure Security Center para proteger suas cargas de trabalho em nuvem híbrida](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>Recursos da comunidade

* [UserVoice da Central de Segurança](https://feedback.azure.com/forums/347535-azure-security-center)
* [Fórum da comunidade da Central de Segurança](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



