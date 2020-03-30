---
title: Alertas de segurança no Azure Security Center | Microsoft Docs
description: Este tópico explica quais são os alertas de segurança e os diferentes tipos disponíveis no Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 697c038a2fefdde8e488dad23a4e38e0b2b7b288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415845"
---
# <a name="security-alerts-in-azure-security-center"></a>Alertas de segurança na Central de Segurança do Azure

No Azure Security Center, há uma variedade de alertas para muitos tipos de recursos diferentes. O Security Center gera alertas para recursos implantados no Azure e também para recursos implantados em ambientes de nuvem híbrida e local.

Os alertas de segurança são acionados por detecções avançadas e estão disponíveis apenas no Nível Padrão do Azure Security Center. Há uma avaliação gratuita disponível. Você pode atualizar na seleção de Tipo de Preço na [Política de Segurança](security-center-pricing.md). Visite a [página do Centro de Segurança](https://azure.microsoft.com/pricing/details/security-center/) para saber mais sobre preços.

## <a name="responding-to-todays-threats"></a>Respondendo às ameaças <a name="respond-threats"> </a> de hoje

Houve alterações significativas no panorama de ameaças nos últimos 20 anos. No passado, as empresas normalmente só tinham que se preocupar com a desfiguração de sites por invasores individuais que estavam mais interessados em ver "o que podiam fazer". Os atacantes de hoje são muito mais sofisticados e organizados. Eles geralmente têm objetivos estratégicos e financeiros específicos. Eles também têm mais recursos disponíveis, já que podem ser financiados por nações ou pelo crime organizado.

Essas realidades em mudança levaram a um nível sem precedentes de profissionalismo nas fileiras de atacantes. Eles não estão mais interessados em desfiguração da Web. Eles agora estão interessados em roubar informações, contas financeiras e dados privados – todos os quais eles podem usar para gerar dinheiro no mercado aberto ou para alavancar uma determinada posição comercial, política ou militar. Ainda mais preocupante que esses invasores com um objetivo financeiro, são os invasores que violam as redes prejudicar a infraestrutura e pessoas.

Em resposta, as organizações geralmente implantam várias soluções pontuais, com foco em defender o perímetro ou os pontos de extremidade da empresa procurando assinaturas de ataques conhecidos. Essas soluções tendem a gerar um alto volume de alertas de baixa fidelidade, que exigem que um analista de segurança faça a triagem e investigue. A maioria das organizações não têm o tempo e o conhecimento necessário para responder a esses alertas; vários ficam sem investigação.  

Além disso, os atacantes desenvolveram seus métodos para subverter muitas defesas baseadas em assinaturas e [adaptar-se a ambientes em nuvem.](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/) Novas abordagens são necessárias para identificar ameaças emergentes e agilizar a detecção e a resposta mais rapidamente.

## <a name="what-are-security-alerts-and-security-incidents"></a>O que são alertas de segurança e incidentes de segurança? 

**Alertas** são as notificações que o Security Center gera quando detecta ameaças em seus recursos. O Security Center prioriza e lista os alertas, juntamente com as informações necessárias para que você investigue rapidamente o problema. A Central de Segurança também fornece recomendações sobre como corrigir um ataque.

**Um incidente** de segurança é uma coleção de alertas relacionados, em vez de listar cada alerta individualmente. O Security Center usa a [Correlação de Alerta Inteligente da Nuvem](security-center-alerts-cloud-smart.md) para correlacionar diferentes alertas e sinais de baixa fidelidade em incidentes de segurança.

Usando incidentes, o Security Center fornece uma visão única de uma campanha de ataque e todos os alertas relacionados. Essa visualização permite que você entenda rapidamente quais ações o invasor tomou e quais recursos foram afetados. Para obter mais informações, consulte [a correlação de alerta inteligente da Nuvem](security-center-alerts-cloud-smart.md).



## <a name="how-does-security-center-detect-threats"></a>Como o Security Center detecta ameaças? <a name="detect-threats"> </a>

Os pesquisadores de segurança da Microsoft estão constantemente à procura de ameaças. Devido à presença global da Microsoft na nuvem e no local, eles têm acesso a um conjunto expansivo de telemetria. A ampla e diversificada coleção de conjuntos de dados permite a descoberta de novos padrões de ataque e tendências em seus produtos de consumo e empresas locais, bem como seus serviços on-line. Como resultado, a Central de Segurança pode atualizar rapidamente seus algoritmos de detecção conforme os invasores lançam explorações novas e cada vez mais sofisticadas. Isso ajuda a acompanhar o ritmo de um ambiente de ameaças que muda rapidamente.

Para detectar ameaças reais e reduzir falsos positivos, o Security Center coleta, analisa e integra dados de log de seus recursos do Azure e da rede. Também trabalha com soluções de parceiros conectados, como soluções de proteção de firewall e endpoint. O Security Center analisa essas informações, muitas vezes correlacionando informações de várias fontes, para identificar ameaças.

![Apresentação e coleta de dados da Central de Segurança](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

A Central de Segurança emprega análise de segurança avançada, que vai além das abordagens baseadas em assinatura. Inovações em tecnologias de big data e [aprendizado de máquina](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são usadas para aproveitar os eventos de avaliação em toda a malha de nuvem, detectando ameaças que seriam impossíveis de identificar usando abordagens manuais e prevendo a evolução de ataques. Essas análises de segurança incluem:

* **Inteligência integrada de ameaças**: a Microsoft tem uma imensa quantidade de inteligência de ameaças globais. A telemetria flui de várias fontes, como o Azure, o Office 365, o Microsoft CRM online, o Microsoft Dynamics AX, o outlook.com, o MSN.com, a DCU (Unidade de Crimes Digitais da Microsoft) e o Microsoft Security Response Center (MSRC). Os pesquisadores também recebem informações de inteligência de ameaças que são compartilhadas entre os principais provedores de serviços em nuvem e feeds de outros terceiros. A Central de Segurança do Azure pode usar essas informações para alertá-lo de ameaças vindas de maus atores conhecidos.

* **Análise comportamental**: A análise comportamental é uma técnica que analisa e compara dados a uma coleção de padrões conhecidos. No entanto, esses padrões não são assinaturas simples. Eles são determinados por meio de algoritmos de aprendizado de máquina complexos que são aplicados a grandes conjuntos de dados. Eles também são determinados pela análise cuidadosa de comportamentos mal-intencionados por analistas especialistas. A Central de Segurança do Azure pode usar a análise de comportamento para identificar recursos comprometidos baseado na análise dos logs de máquina virtual, dos logs de dispositivo de rede virtual, dos logs da malha, dos despejos de memória e de outras fontes.

* **Detecção de anomalias**: O Azure Security Center também usa a detecção de anomalias para identificar ameaças. Em contraste com a análise comportamental (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a detecção de anomalias é mais "personalizada" e se concentra em linhas de base específicas para suas implantações. O aprendizado de máquina é aplicado para determinar a atividade normal das implantações e, em seguida, as regras são geradas para definir condições de exceção que possam representar um evento de segurança.

## <a name="how-are-alerts-classified"></a>Como os alertas são classificados?

O Security Center atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você atende a cada alerta, para que quando um recurso estiver comprometido, você possa obtê-lo imediatamente. A gravidade é baseada no quão confiante o Security Center está na descoberta ou no analítico usado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

> [!NOTE]
> A gravidade do alerta é exibida de forma diferente no portal e nas versões da API REST que antecedem 01-01-2019. Se você estiver usando uma versão mais antiga da API, atualize para a experiência consistente descrita abaixo.

- **Alta:** Há uma grande probabilidade de que seu recurso esteja comprometido. Você deve analisá-lo imediatamente. A Central de Segurança tem alta confiança em ambas as más intenções e em descobertas usadas para emitir o alerta. Por exemplo, um alerta que detecta a execução de uma ferramenta mal-intencionada conhecida como Mimikatz, uma ferramenta comum usada para roubo de credenciais.
- **Médio:** Esta é provavelmente uma atividade suspeita pode indicar que um recurso está comprometido.
A confiança do Security Center no analytic ou na descoberta é média e a confiança da intenção maliciosa é de médio a alto. Normalmente seriam detecções baseadas em aprendizado de máquina ou de forma com anomalias. Por exemplo, uma tentativa de login de um local anômalo.
- **Baixo:** Pode ser um ataque benigno positivo ou bloqueado.
   * A Central de Segurança não está confiante o suficiente para que a intenção seja mal-intencionada e a atividade possa ser inocente. Por exemplo, limpar log é uma ação que pode acontecer quando um invasor tenta ocultar seus rastros, mas em muitos casos é uma operação de rotina executada por administradores.
   * O Centro de Segurança não costuma dizer quando os ataques foram bloqueados, a menos que seja um caso interessante que sugerimos que você investigue. 
- **Informativo:** Você só verá alertas informativos quando você for abaixo de um incidente de segurança ou se você usar a API REST com um ID de alerta específico. Um incidente geralmente é composto de um número de alertas, alguns dos quais podem aparecer por conta própria e são apenas informativo, mas no contexto de outros alertas podem ser dignos de uma análise mais detalhada. 

## <a name="continuous-monitoring-and-assessments"></a>Monitoramento e avaliações contínuas

O Azure Security Center se beneficia de ter equipes de pesquisa de segurança e ciência de dados em toda a Microsoft que monitoram continuamente as mudanças no cenário de ameaças. Isso inclui as seguintes iniciativas:

* **Monitoramento de inteligência de ameaças**: A inteligência de ameaças inclui mecanismos, indicadores, implicações e conselhos acionáveis sobre ameaças existentes ou emergentes. Essas informações são compartilhadas na comunidade de segurança e a Microsoft monitora continuamente os feeds de inteligência contra ameaças de fontes internas e externas.
* **Compartilhamento de sinal**: Insights das equipes de segurança em todo o amplo portfólio de serviços de nuvem e on-premises da Microsoft, servidores e dispositivos de ponto final do cliente são compartilhados e analisados.
* **Especialistas de segurança da Microsoft**: comprometimento contínuo com as equipes da Microsoft que trabalham em campos de segurança especializada, como computação forense e detecção de ataque à Web.
* **Ajuste de detecção**: algoritmos são executados em conjuntos de dados de clientes reais e pesquisadores de segurança trabalham com clientes para validar os resultados. Verdadeiros e falsos positivos são usados para refinar os algoritmos de aprendizado de máquina.

Esses esforços combinados culminam em detecções novas e aprimoradas, das quais você pode se beneficiar instantaneamente – não há ação para você tomar.


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os diferentes tipos de alertas disponíveis no Security Center. Para obter mais informações, consulte:

* [Proteção contra ameaças no Azure Security Center](threat-protection.md) - Para uma breve descrição das fontes dos alertas de segurança exibidos pelo Azure Security Center 
* **Alertas de segurança no Azure Activity Log** - Além de estarem disponíveis no portal Azure ou programáticamente, alertas de segurança e incidentes são auditados como eventos no [Azure Activity Log](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view). Para obter mais informações sobre o esquema de eventos, consulte [Alertas de segurança no registro de atividades do Azure](https://go.microsoft.com/fwlink/?linkid=2114113)

