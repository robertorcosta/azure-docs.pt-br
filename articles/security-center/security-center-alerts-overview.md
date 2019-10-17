---
title: Alertas de segurança na central de segurança do Azure | Microsoft Docs
description: Este tópico explica o que são os alertas de segurança e os diferentes tipos disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: memildin
ms.openlocfilehash: 69b81417e541bd6853e02065e8cee08e3e04b4a2
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433889"
---
# <a name="security-alerts-in-azure-security-center"></a>Alertas de segurança na Central de Segurança do Azure

Na central de segurança do Azure, há uma variedade de alertas para vários tipos de recursos diferentes. A central de segurança gera alertas para recursos implantados no Azure e também para recursos implantados em ambientes de nuvem híbrida e locais.

Detecções avançadas estão disponíveis na camada Standard da Central de Segurança do Azure. Há uma avaliação gratuita disponível. Você pode atualizar na seleção de Tipo de Preço na [Política de Segurança](security-center-pricing.md). Visite a [página do Centro de Segurança](https://azure.microsoft.com/pricing/details/security-center/) para saber mais sobre preços.

## Respondendo às ameaças <a name="respond-threats"></a> atuais

Houve alterações significativas no panorama de ameaças nos últimos 20 anos. Antigamente, as empresas normalmente só precisavam preocupar-se com a desfiguração do site por invasores individuais, que basicamente tinham interesse em ver “o que poderiam fazer”. Os hackers de hoje em dia são muito mais sofisticados e organizados. Eles geralmente têm objetivos estratégicos e financeiros específicos. Eles também têm mais recursos disponíveis, já que podem ser financiados por nações ou pelo crime organizado.

Essas realidades em constante mudança levaram a um nível incomparável de profissionalismo nas classificações de invasor. Eles não estão mais interessados em desfiguração da Web. Agora eles estão interessados em roubar informações, contas financeiras e dados privados – todos eles podem usar para gerar dinheiro no mercado aberto ou para aproveitar uma posição empresarial, política ou militar específica. Ainda mais preocupante que esses invasores com um objetivo financeiro, são os invasores que violam as redes prejudicar a infraestrutura e pessoas.

Em resposta, as organizações geralmente implantam várias soluções pontuais, com foco em defender o perímetro ou os pontos de extremidade da empresa procurando assinaturas de ataques conhecidos. Essas soluções tendem a gerar um alto volume de alertas de baixa fidelidade, que exigem que um analista de segurança faça a triagem e investigue. A maioria das organizações não têm o tempo e o conhecimento necessário para responder a esses alertas; vários ficam sem investigação.  

Além disso, os invasores evoluíram seus métodos para subverter muitas defesas baseadas em assinatura e [adaptar-se a ambientes de nuvem](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Novas abordagens são necessárias para identificar ameaças emergentes e agilizar a detecção e a resposta mais rapidamente.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?

Alertas são as notificações que a central de segurança gera quando detecta ameaças em seus recursos. A central de segurança prioriza e lista os alertas, juntamente com as informações necessárias para que você investigue rapidamente o problema. A central de segurança também fornece recomendações sobre como você pode corrigir um ataque.

## Como a central de segurança detecta ameaças? <a name="detect-threats"> </a>

Os pesquisadores de segurança da Microsoft estão constantemente à procura de ameaças. Devido à presença global da Microsoft na nuvem e no local, eles têm acesso a um conjunto extenso de telemetria. A coleção abrangente e diversificada de conjuntos de valores permite a descoberta de novos padrões de ataque e tendências em seus produtos de consumidores e empresas locais, bem como seu serviços online. Como resultado, a Central de Segurança pode atualizar rapidamente seus algoritmos de detecção conforme os invasores lançam explorações novas e cada vez mais sofisticadas. Isso ajuda a acompanhar o ritmo de um ambiente de ameaças que muda rapidamente.

Para detectar ameaças reais e reduzir falsos positivos, a central de segurança coleta, analisa e integra dados de log de seus recursos do Azure e da rede. Ele também funciona com soluções de parceiros conectadas, como firewall e soluções de proteção de ponto de extremidade. A central de segurança analisa essas informações, geralmente correlacionando informações de várias fontes, para identificar ameaças.

![Apresentação e coleta de dados da Central de Segurança](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

A Central de Segurança emprega análise de segurança avançada, que vai além das abordagens baseadas em assinatura. Inovações em tecnologias de big data e [aprendizado de máquina](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são usadas para aproveitar os eventos de avaliação em toda a malha de nuvem, detectando ameaças que seriam impossíveis de identificar usando abordagens manuais e prevendo a evolução de ataques. Essas análises de segurança incluem:

* **Inteligência contra ameaças integrada**: procura atores ruins, aproveitando a inteligência contra ameaças globais dos produtos e serviços da Microsoft, da DCU (unidade de crimes digitais) da Microsoft, do MSRC (Microsoft Security Response Center) e de feeds externos.
* **Análise comportamental**: aplica padrões conhecidos para descobrir comportamentos mal-intencionados.
* **Detecção de anomalias**: usa estatísticas de criação de perfil para criar uma linha de base histórica. Ela o alertará sobre desvios das linhas de base estabelecidas em conformidade com um vetor de possível ataque.

As seções a seguir discutem cada uma dessas análises com mais detalhes.

### <a name="integrated-threat-intelligence"></a>Inteligência de ameaças integrada

A Microsoft tem uma grande quantidade de inteligência contra ameaças globais. A telemetria flui de várias fontes, como o Azure, o Office 365, o Microsoft CRM online, o Microsoft Dynamics AX, o outlook.com, o MSN.com, a DCU (Unidade de Crimes Digitais da Microsoft) e o Microsoft Security Response Center (MSRC). Os pesquisadores também recebem informações de inteligência contra ameaças que são compartilhadas entre os principais provedores de serviços de nuvem e os feeds de terceiros. A Central de Segurança do Azure pode usar essas informações para alertá-lo de ameaças vindas de maus atores conhecidos.

### <a name="behavioral-analytics"></a>Análise comportamental

A análise de comportamento é uma técnica que analisa e compara dados em uma coleção de padrões conhecidos. No entanto, esses padrões não são assinaturas simples. Eles são determinados por meio de algoritmos de aprendizado de máquina complexos que são aplicados a grandes conjuntos de dados. Eles também são determinados pela análise cuidadosa de comportamentos mal-intencionados por analistas especialistas. A Central de Segurança do Azure pode usar a análise de comportamento para identificar recursos comprometidos baseado na análise dos logs de máquina virtual, dos logs de dispositivo de rede virtual, dos logs da malha, dos despejos de memória e de outras fontes.

Além disso, há correlação com outros sinais para verificar a evidência de suporte de uma campanha generalizada. Essa correlação ajuda a identificar os eventos que são consistentes com os indicadores de comprometimento estabelecidos. 

### <a name="anomaly-detection"></a>Detecção de anomalias

A Central de Segurança do Azure também usa detecção de anomalias para identificar ameaças. Ao contrário da análise de comportamento (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a detecção de anomalias é mais "personalizada" e se concentra nas linhas de base que são específicas das suas implantações. O aprendizado de máquina é aplicado para determinar a atividade normal das implantações e, em seguida, as regras são geradas para definir condições de exceção que possam representar um evento de segurança.

## <a name="how-are-alerts-classified"></a>Como os alertas são classificados?

A central de segurança atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você participa de cada alerta, para que, quando um recurso for comprometido, você possa acessá-lo imediatamente. A gravidade se baseia em quão confiante a central de segurança está na localização ou análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

> [!NOTE]
> A severidade do alerta é exibida diferente no portal e a API REST, as diferenças são indicadas na lista abaixo.

* **Alta:** Há uma alta probabilidade de o recurso ser comprometido. Você deve analisá-lo imediatamente. A Central de Segurança tem alta confiança em ambas as más intenções e em descobertas usadas para emitir o alerta. Por exemplo, um alerta que detecta a execução de uma ferramenta mal-intencionada conhecida como Mimikatz, uma ferramenta comum usada para roubo de credenciais.
* **Média (baixa na API REST)** : provavelmente, uma atividade suspeita pode indicar que um recurso está comprometido.
Confiança da Central de Segurança na análise ou localizar é médio e a confiança de más intenções é médio a alto. Normalmente, elas seriam aprendizado de máquina ou detecções baseadas em anomalias. Por exemplo, uma tentativa de entrada de um local anormal.
* **Baixa (informações na API REST)** : isso pode ser um positivo benigno ou um ataque bloqueado.
   * A Central de Segurança não está confiante o suficiente para que a intenção seja mal-intencionada e a atividade possa ser inocente. Por exemplo, limpar log é uma ação que pode acontecer quando um invasor tenta ocultar seus rastros, mas em muitos casos é uma operação de rotina executada por administradores.
   * A Central de Segurança geralmente não informa quando ataques foram bloqueados, a menos que seja um caso interessante que sugerimos que você examine. 
* **Informativo (silencioso na API REST)** : você só verá alertas informativos ao fazer uma busca detalhada em um incidente de segurança ou se você usar a API REST com uma determinada ID de alerta. Um incidente geralmente é composto de um número de alertas, alguns dos quais podem aparecer por conta própria e são apenas informativo, mas no contexto de outros alertas podem ser dignos de uma análise mais detalhada. 
 

## <a name="continuous-monitoring-and-assessments"></a>Monitoramento e avaliações contínuos

A central de segurança do Azure beneficia-se de ter equipes de pesquisa de segurança e ciência de dados em toda a Microsoft que monitoram continuamente as alterações no cenário de ameaças. Isso inclui as seguintes iniciativas:

* **Monitoramento de inteligência contra ameaças**: a inteligência contra ameaças inclui mecanismos, indicadores, implicações e conselhos acionáveis sobre ameaças iminentes ou existentes. Essas informações são compartilhadas na comunidade de segurança e a Microsoft monitora continuamente os feeds de inteligência contra ameaças de fontes internas e externas.
* **Compartilhamento de sinal**: ideias de equipes de segurança de todo o amplo portfólio da Microsoft de serviços locais e de nuvem, servidores e dispositivos de ponto de extremidade cliente são compartilhadas e analisadas.
* **Especialistas de segurança da Microsoft**: comprometimento contínuo com as equipes da Microsoft que trabalham em campos de segurança especializada, como computação forense e detecção de ataque à Web.
* **Ajuste de detecção**: algoritmos são executados em conjuntos de dados de clientes reais e pesquisadores de segurança trabalham com clientes para validar os resultados. Verdadeiros e falsos positivos são usados para refinar os algoritmos de aprendizado de máquina.

Esses esforços combinados culminam em detecções novas e aprimoradas de que você pode se beneficiar instantaneamente. Não há nenhuma ação a ser tomada.

## Tipos <a name="security-alert-types"></a> de alertas de segurança

Os tópicos a seguir orientam você pelos diferentes alertas, de acordo com os tipos de recursos:

* [Alertas de VMs e servidores IaaS](security-center-alerts-iaas.md)
* [Alertas de computação nativos](security-center-alerts-compute.md)
* [Alertas de serviços de dados](security-center-alerts-data-services.md)

Os tópicos a seguir explicam como a central de segurança usa a telemetria diferente que ela coleta da integração com a infraestrutura do Azure, para aplicar camadas de proteção adicionais para recursos implantados no Azure:

* [Alertas da camada de serviço](security-center-alerts-service-layer.md)
* [Integração com os produtos de segurança do Azure](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>O que são incidentes de segurança?

Um incidente de segurança é uma coleção de alertas relacionados, em vez de listar cada alerta individualmente. A central de segurança usa a [correlação de alertas inteligentes na nuvem](security-center-alerts-cloud-smart.md) para correlacionar diferentes alertas e sinais de baixa fidelidade em incidentes de segurança.

Usando incidentes, a central de segurança fornece uma única exibição de uma campanha de ataque e todos os alertas relacionados. Essa exibição permite que você entenda rapidamente as ações tomadas pelo invasor e quais recursos foram afetados. Para obter mais informações, consulte [correlação de alertas inteligentes na nuvem](security-center-alerts-cloud-smart.md).

## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu sobre os diferentes tipos de alertas disponíveis na central de segurança. Para obter mais informações, veja:

* [Guia de planejamento e operações da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Perguntas Frequentes sobre a Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-faq)

