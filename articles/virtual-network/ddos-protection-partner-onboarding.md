---
title: Parceria com o Azure DDoS Protection Standard
description: Entenda as oportunidades de parceria habilitadas pelo Azure DDoS Protection Standard.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849676"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Parceria com o Azure DDoS Protection Standard
Este artigo descreve oportunidades de parceria habilitadas pelo Azure DDoS Protection Standard. Este artigo foi projetado para ajudar os gerentes de produto e as funções de desenvolvimento de negócios a entender os caminhos de investimento e fornecer insights sobre as propostas de valor de parceria.

## <a name="background"></a>Segundo plano
Os ataques de negação distribuída de serviço (DDoS) são uma das principais preocupações de disponibilidade e segurança expressas pelos clientes que movem seus aplicativos para a nuvem. Com extorsão e hacktivismo sendo as motivações comuns por trás dos ataques DDoS, eles têm aumentado consistentemente em tipo, escala e frequência de ocorrência, pois são relativamente fáceis e baratos de lançar.

O Azure DDoS Protection fornece contramedidas contra as ameaças DDoS mais sofisticadas, aproveitando a escala global da rede Azure. O serviço fornece recursos aprimorados de mitigação de DDoS para aplicativos e recursos implantados em redes virtuais.

Os parceiros de tecnologia podem proteger os recursos de seus clientes nativamente com o Azure DDoS Protection Standard para resolver as preocupações de disponibilidade e confiabilidade devido aos ataques DDoS.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Introdução ao Padrão de Proteção Azure DDoS
O Azure DDoS Protection Standard fornece recursos avançados de mitigação DDoS contra ataques DDoS de camada 3 e de camada 4. A seguir estão as principais características do serviço DDoS Protection Standard.

### <a name="adaptive-real-time-tuning"></a>Ajuste adaptável em tempo real
Para cada aplicativo protegido, o Azure DDoS Protection Standard ajusta automaticamente os limites da política de mitigação DDoS com base nos padrões de perfil de tráfego do aplicativo. O serviço realiza essa personalização usando dois insights:

- Aprendizado automático de padrões de tráfego por cliente (por IP) das camadas 3 e 4.
- Minimizando falsos positivos, considerando que a escala do Azure permite que absorva uma quantidade significativa de tráfego.

![Sintonia adaptativa em tempo real](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Análise de ataques, telemetria, monitoramento e alerta
O Azure DDoS Protection identifica e mitiga ataques DDoS sem qualquer intervenção do usuário.

- Se o recurso protegido estiver na assinatura coberta pelo Azure Security Center, o DDoS Protection Standard envia automaticamente um alerta ao Security Center sempre que um ataque DDoS for detectado e mitigado contra o aplicativo protegido.
- Alternativamente, para ser notificado quando houver uma mitigação ativa para um IP público protegido, você pode [configurar um alerta](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) na métrica ataque DDoS ou não.
- Além disso, você pode optar por criar alertas para as outras métricas DDoS e [configurar análises](manage-ddos-protection.md#configure-ddos-attack-analytics) de ataque para entender a escala do ataque, tráfego sendo descartado, vetores de ataque, principais colaboradores e outros detalhes.

![Métricas DDoS](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>Resposta rápida DDoS (DRR)
Os clientes do DDoS Protection Standard têm acesso à [equipe de resposta rápida](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) durante um ataque ativo. O DRR pode ajudar na investigação de ataque, mitigações personalizadas durante um ataque e análise pós-ataque.

### <a name="sla-guarantee-and-cost-protection"></a>Garantia de SLA e proteção de custos
O serviço DDoS Protection Standard é coberto por um SLA de 99,99% e a proteção de custos fornece créditos de recursos para a escala durante um ataque documentado. Para obter mais informações, consulte [SLA para Proteção DDoS do Azure](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Cenários de parceiros em destaque
A seguir, os principais benefícios que você pode obter ao integrar-se com o Azure DDoS Protection Standard:

- Os serviços oferecidos pelos parceiros (balanceador de carga, firewall de aplicativos web, firewall, etc.) aos seus clientes são automaticamente protegidos (rotulados em branco) pelo Azure DDoS Protection Standard no back-end.
- Os parceiros têm acesso às análises de ataque e telemetria padrão de proteção Do Azure DDoS que podem integrar com seus próprios produtos, oferecendo uma experiência unificada ao cliente.  
- Os parceiros têm acesso ao suporte de resposta rápida DDoS mesmo na ausência de resposta rápida do Azure, para problemas relacionados ao DDoS.
- Os aplicativos protegidos dos parceiros são apoiados por uma garantia De SLA DDoS e proteção de custos em caso de ataques DDoS.

## <a name="technical-integration-overview"></a>Visão geral da integração técnica
As oportunidades de parceria do Azure DDoS Protection Standard são disponibilizadas através do portal Azure, APIs e CLI/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integre-se ao padrão de proteção DDoS
As seguintes etapas são necessárias para que os parceiros configurem a integração com o Azure DDoS Protection Standard:
1. Crie um Plano de Proteção DDoS na assinatura desejada (parceiro). Para obter instruções passo a passo, consulte [Criar um plano de proteção padrão DDoS](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Apenas 1 Plano de Proteção DDoS precisa ser criado para um determinado inquilino. 
2. Implante um serviço com ponto final público em suas assinaturas (parceiras), como balanceador de carga, firewalls e firewall de aplicativos web. 
3. Habilite o Azure DDoS Protection Standard na rede virtual do serviço que possui pontos finais públicos usando o Plano de Proteção DDoS criado na primeira etapa. Para obter instruções stpe-by-step, consulte [Ativar o plano de proteção padrão DDoS](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > Depois que o Azure DDoS Protection Standard é ativado em uma rede virtual, todos os IPs públicos dentro dessa rede virtual são automaticamente protegidos. A origem desses IPs públicos pode ser dentro do Azure (assinatura do cliente) ou fora do Azure. 
4. Opcionalmente, integre a telemetria padrão de proteção Do Azure DDoS e análise de ataque no painel de controle específico do aplicativo. Para obter mais informações sobre o uso da telemetria, consulte [Use telemetria de proteção DDoS](manage-ddos-protection.md#use-ddos-protection-telemetry). Para obter mais informações sobre a configuração de análises de ataque, consulte [Configurar análises de ataque DDoS](manage-ddos-protection.md#configure-ddos-attack-analytics)

### <a name="onboarding-guides-and-technical-documentation"></a>Guias de onboarding e documentação técnica

- [Página do produto Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)
- [Documentação de proteção Do Zure DDoS](ddos-protection-overview.md)
- [Referência da API de proteção DDoS do Azure](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Referência em API de rede virtual do Azure](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Obter ajuda

- Se você tiver dúvidas sobre integrações de aplicativos, serviços ou produtos com o Azure DDoS Protection Standard, entre em contato com a [comunidade de segurança do Azure](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security).
- Acompanhe as discussões sobre [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Chegar ao mercado

- O principal programa de parceria com a Microsoft é o [Microsoft Partner Network](https://partner.microsoft.com/). – As integrações do Microsoft Graph Security caem na faixa [ISV (Independent Software Vendor, fornecedor independente de software do MPN).](https://partner.microsoft.com/saas-solution-guide)
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) é o programa especificamente para os Parceiros de Segurança da Microsoft para ajudar a enriquecer seus produtos de segurança e melhorar a capacidade de descoberta do cliente de suas integrações aos produtos Microsoft Security.

## <a name="next-steps"></a>Próximas etapas
Exibir integrações de parceiros existentes:

- [Barracuda WAF-as-a-service](https://www.barracuda.com/waf-as-a-service)
- [WaF da Nuvem Azul da Radware](https://www.radware.com/resources/microsoft-azure/)
