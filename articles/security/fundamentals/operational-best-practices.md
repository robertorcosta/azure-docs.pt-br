---
title: Práticas recomendadas de segurança para seus ativos do Azure
titleSuffix: Azure security
description: Este artigo fornece um conjunto de práticas recomendadas operacionais para proteger seus dados, aplicativos e outros ativos no Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 72d7a2dd112e5e7a5105ff977e3917ccdfd7b53e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500299"
---
# <a name="azure-operational-security-best-practices"></a>Práticas recomendadas de Segurança Operacional do Azure
Este artigo fornece um conjunto de práticas recomendadas operacionais para proteger seus dados, aplicativos e outros ativos no Azure.

As recomendações baseiam-se um consenso de opinião, e trabalhar com recursos da plataforma Windows Azure atuais e conjuntos de recursos. Opiniões e tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas mudanças.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Defina e implante fortes práticas de segurança operacional
A segurança operacional do Azure refere-se a serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros ativos no Azure. A segurança operacional do Azure se baseia em uma estrutura que incorpora o conhecimento adquirido por recursos exclusivos da Microsoft, incluindo [o SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl), o programa [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) e o conhecimento profundo do cenário de ameaças de segurança cibernética.

## <a name="manage-and-monitor-user-passwords"></a>Gerencie e monitore senhas de usuário
A tabela a seguir lista algumas práticas recomendadas relacionadas ao gerenciamento de senhas de usuário:

**Melhores práticas**: Certifique-se de ter o nível adequado de proteção por senha na nuvem.   
**Detalhe**: Siga a orientação no [Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance/), que é escopo para usuários das plataformas de identidade da Microsoft (Azure Active Directory, Active Directory e Conta Microsoft).

**Melhores práticas**: Monitore ações suspeitas relacionadas às suas contas de usuário.   
**Detalhe**: Monitorpara [usuários em risco](/azure/active-directory/reports-monitoring/concept-user-at-risk) e [logins arriscados](../../active-directory/reports-monitoring/concept-risk-events.md) usando relatórios de segurança do Azure AD.

**Melhor prática**: Detectar e remediar senhas de alto risco automaticamente.   
**Detalhe**: [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview) é um recurso da edição Azure AD Premium P2 que permite:

- Detecte potenciais vulnerabilidades que afetam as identidades da sua organização
- Configurar respostas automatizadas para ações suspeitas detectadas que se relacionem com as identidades da sua organização
- Investigue incidentes suspeitos e tome as medidas apropriadas para resolvê-los

## <a name="receive-incident-notifications-from-microsoft"></a>Receba notificações de incidentes da Microsoft
Certifique-se de que sua equipe de operações de segurança receba notificações de incidentes do Azure da Microsoft. Uma notificação de incidente permite que sua equipe de segurança saiba que você comprometeu os recursos do Azure para que eles possam responder e remediar rapidamente potenciais riscos de segurança.

No portal de inscrição do Azure, você pode garantir que as informações de contato do administrador incluam detalhes que notifiquem as operações de segurança. Informações de contato são um número de telefone e um endereço de email.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organize as assinaturas do Azure em grupos de gestão
Se a organização tiver muitas assinaturas, talvez seja necessário gerenciar de maneira eficiente o acesso, as políticas e a conformidade dessas assinaturas. [Os grupos de gerenciamento do Azure](/azure/governance/management-groups/create) fornecem um nível de escopo acima das assinaturas. Você organiza assinaturas em contêineres chamados grupos de gerenciamento e aplica suas condições de governança aos grupos de gerenciamento. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as condições aplicadas ao grupo de gerenciamento.

Você pode construir uma estrutura flexível de grupos de gerenciamento e assinaturas em um diretório. Cada diretório recebe um único grupo de gerenciamento de alto nível chamado grupo de gerenciamento raiz. Esse grupo de gerenciamento raiz é compilado na hierarquia para que todos os grupos de gerenciamento e assinaturas sejam dobrados nele. O grupo de gerenciamento raiz permite que políticas globais e atribuições de RBAC sejam aplicadas no nível do diretório.

Aqui estão algumas práticas recomendadas para usar grupos de gestão:

**Melhores práticas**: Certifique-se de que novas assinaturas apliquem elementos de governança, como políticas e permissões à medida que são adicionadas.   
**Detalhe**: Use o grupo de gerenciamento raiz para atribuir elementos de segurança em toda a empresa que se aplicam a todos os ativos do Azure. Políticas e permissões são exemplos de elementos.

**Melhores práticas**: Alinhar os principais níveis dos grupos de gestão com a estratégia de segmentação para fornecer um ponto de controle e consistência de políticas dentro de cada segmento.   
**Detalhe**: Crie um único grupo de gestão para cada segmento o grupo de gerenciamento raiz. Não crie outros grupos de gerenciamento a raiz.

**Melhores práticas**: Limitar a profundidade do grupo de gerenciamento para evitar confusão que dificulte tanto as operações quanto a segurança.   
**Detalhe:** Limite sua hierarquia a três níveis, incluindo a raiz.

**Melhores práticas**: Selecione cuidadosamente quais itens aplicar para toda a empresa com o grupo de gerenciamento raiz.   
**Detalhe**: Certifique-se de que os elementos do grupo de gerenciamento raiz tenham uma necessidade clara de serem aplicados em todos os recursos e que sejam de baixo impacto.

Bons candidatos incluem:

- Requisitos regulatórios que tenham um claro impacto nos negócios (por exemplo, restrições relacionadas à soberania de dados)
- Requisitos com impacto negativo potencial próximo de zero nas operações, como política com efeito de auditoria ou atribuições de permissão rbac que foram cuidadosamente revisadas

**Práticas recomendadas**: Planeje e teste cuidadosamente todas as mudanças em toda a empresa no grupo de gerenciamento raiz antes de aplicá-las (política, modelo RBAC e assim por diante).   
**Detalhe**: As alterações no grupo de gerenciamento raiz podem afetar todos os recursos do Azure. Embora eles forneçam uma maneira poderosa de garantir a consistência em toda a empresa, erros ou uso incorreto podem afetar negativamente as operações de produção. Teste todas as alterações no grupo de gerenciamento de raízes em um laboratório de teste ou piloto de produção.

## <a name="streamline-environment-creation-with-blueprints"></a>Simplifique a criação de ambientes com projetos
O serviço [Azure Blueprints](/azure/governance/blueprints/overview) permite que arquitetos em nuvem e grupos centrais de tecnologia da informação definam um conjunto repetível de recursos do Azure que implementa e adere aos padrões, padrões e requisitos de uma organização. O Azure Blueprints permite que as equipes de desenvolvimento construam e se levantem rapidamente em novos ambientes com um conjunto de componentes incorporados e a confiança de que estão criando esses ambientes dentro da conformidade organizacional.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorar serviços de armazenamento quanto a mudanças inesperadas no comportamento
Questões de diagnóstico e de solução de problemas em um aplicativo distribuído hospedado em um ambiente de nuvem podem ser mais complexas que em ambientes tradicionais. Aplicativos podem ser implantados em uma infraestrutura PaaS ou IaaS, local, em um dispositivo móvel ou em alguma combinação desses ambientes. Tráfego de rede do seu aplicativo pode passar por redes públicas e privadas, e seu aplicativo poderá usar várias tecnologias de armazenamento.

Você deve monitorar continuamente os serviços de armazenamento que o aplicativo usa para qualquer mudança inesperada em comportamento (como tempos de resposta mais lentos). Use o log para coletar dados mais detalhados e analisar o problema em profundidade. As informações de diagnósticos que você obtiver tanto do monitoramento quanto do registro em log o ajudarão a determinar a raiz do problema que o seu aplicativo encontrou. Você poderá solucionar o problema e determinar as etapas apropriadas para corrigi-lo.

A [Análise de Armazenamento do Azure](../../storage/common/storage-analytics.md) executa o registro em log e fornece dados de métrica para uma conta de armazenamento do Azure. Recomendamos que você use esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento.

## <a name="prevent-detect-and-respond-to-threats"></a>Evitar, detectar e reagir a ameaças
[O Azure Security Center](../../security-center/security-center-intro.md) ajuda você a prevenir, detectar e responder a ameaças, fornecendo maior visibilidade (e controle sobre) a segurança de seus recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com um diversas soluções de segurança.

O nível gratuito do Security Center oferece segurança limitada apenas para os recursos do Azure. A camada Standard estende esses recursos para locais e outras nuvens. O Security Center Standard ajuda você a encontrar e corrigir vulnerabilidades de segurança, aplicar controles de acesso e aplicativos para bloquear atividades maliciosas, detectar ameaças usando análises e inteligência e responder rapidamente quando estiver ataque. Você pode experimentar a Central de Segurança Standard sem nenhum custo pelos primeiros 60 dias. Recomendamos que você [atualize sua assinatura do Azure para o Security Center Standard](../../security-center/security-center-get-started.md).

Use o Security Center para obter uma visão central do estado de segurança de todos os seus recursos do Azure. Verifique rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, além disso, identifique com rapidez os recursos que exigem atenção.

O Security Center também se integra ao [ATP (AtC) (Final Threat Protection, proteção de ameaças avançadas) do Microsoft Defender,](../../security-center/security-center-wdatp.md)que fornece recursos abrangentes de Detecção e Resposta de Endpoint (EDR). Com a integração do Microsoft Defender ATP, você pode detectar anormalidades. Você também pode detectar e responder a ataques avançados em pontos finais de servidor monitorados pelo Security Center.

Quase todas as organizações corporativas têm um sistema de gerenciamento de informações e eventos de segurança (SIEM) para ajudar a identificar ameaças emergentes, consolidando informações de log de diversos dispositivos de coleta de sinais. Os logs são então analisados por um sistema de análise de dados para ajudar a identificar o que é "interessante" a partir do ruído que é inevitável em todas as soluções de coleta e análise de logs.

[O Azure Sentinel](/azure/sentinel/overview) é uma solução escalável, nativa na nuvem, de gerenciamento de informações de segurança e eventos (SIEM) e de uma solução de resposta automatizada de orquestração de segurança (SOAR). O Azure Sentinel fornece análises inteligentes de segurança e inteligência de ameaças por meio de detecção de alertas, visibilidade de ameaças, caça proativa e resposta automatizada de ameaças.

Aqui estão algumas práticas recomendadas para prevenir, detectar e responder a ameaças:

**Práticas recomendadas**: Aumente a velocidade e a escalabilidade da sua solução SIEM usando um SIEM baseado em nuvem.   
**Detalhe**: Investigue os recursos e recursos do [Azure Sentinel](/azure/sentinel/overview) e compare-os com as capacidades do que você está usando no local. Considere adotar o Azure Sentinel se ele atender aos requisitos de SIEM da sua organização.

**Melhores práticas**: Encontre as vulnerabilidades de segurança mais graves para que você possa priorizar a investigação.   
**Detalhe**: Revise sua [pontuação segura do Azure](../../security-center/security-center-secure-score.md) para ver as recomendações resultantes das políticas e iniciativas do Azure incorporadas ao Azure Security Center. Essas recomendações ajudam a lidar com os principais riscos, como atualizações de segurança, proteção contra ponto final, criptografia, configurações de segurança, WAF ausente, VMs conectados à Internet e muito mais.

A pontuação segura, que é baseada nos controles do Center for Internet Security (CIS), permite que você faça benchmarkda segurança do Azure da sua organização em relação a fontes externas. A validação externa ajuda a validar e enriquecer a estratégia de segurança da sua equipe.

**Melhores práticas**: Monitorar a postura de segurança de máquinas, redes, armazenamento e serviços de dados e aplicativos para descobrir e priorizar possíveis problemas de segurança.  
**Detalhe**: Siga as [recomendações de segurança](../../security-center/security-center-recommendations.md) no Security Center a partir, com os itens de maior prioridade.

**Melhores práticas**: Integre alertas do Security Center em sua solução de gerenciamento de informações de segurança e eventos (SIEM).   
**Detalhe**: A maioria das organizações com um SIEM usa-o como uma central de compensação para alertas de segurança que requerem uma resposta de analistas. Os eventos processados produzidos pelo Security Center são publicados no Azure Activity Log, um dos logs disponíveis através do Azure Monitor. O Azure Monitor oferece um pipeline consolidado para qualquer um dos seus dados de monitoramentos de roteamento para uma ferramenta do SIEM. Consulte [Integrar soluções de segurança no Security Center](../../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) para obter instruções. Se você estiver usando o Azure Sentinel, consulte [Connect Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Melhores práticas**: Integre os logs do Azure com o seu SIEM.   
**Detalhe**: Use [o Monitor Azure para coletar e exportar dados](/azure/azure-monitor/overview#integrate-and-export-data). Essa prática é fundamental para permitir a investigação de incidentes de segurança e a retenção de registros on-line é limitada. Se você estiver usando o Azure Sentinel, consulte [Conectar fontes de dados](../../sentinel/connect-data-sources.md).

**Práticas recomendadas**: Acelere seus processos de investigação e caça e reduza falsos positivos integrando recursos de Detecção e Resposta ao Endpoint (EDR) em sua investigação de ataque.   
**Detalhe**: [Habilite a integração do Microsoft Defender ATP](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration) através da política de segurança do Security Center. Considere usar o Azure Sentinel para caça a ameaças e resposta a incidentes.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Acompanhar monitoramento de rede baseado em cenário de ponta a ponta
Os clientes criam uma rede de ponta a ponta no Azure, combinando recursos de rede como rede virtual, ExpressRoute, Gateway de Aplicativo e balanceadores de carga. O monitoramento está disponível em cada um dos recursos da rede.

O [Observador de Rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional. Use suas ferramentas de diagnóstico e visualização para monitorar e diagnosticar condições em um nível de cenário de rede no Azure, para o Azure e do Azure.

A seguir estão as melhores práticas para as ferramentas disponíveis e monitoramento de rede.

**Melhor prática**: automatize o monitoramento remoto de rede com a captura de pacote.  
**Detalhe**: monitore e realize o diagnóstico de problemas de rede sem fazer logon em suas VMs usando o Observador de Rede. Disparar [captura de pacote](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) por meio da configuração de alertas e obter acesso a informações de desempenho em tempo real no nível de pacote. Ao ver um problema, você poderá investigar os detalhes para um diagnóstico melhor.

**Prática recomendada**: obtenha insight sobre seu tráfego de rede usando logs de fluxo.  
**Detalhe**: desenvolva um entendimento aprofundado sobre padrões de tráfego de rede usando os [logs de fluxo do grupo de segurança de rede](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). As informações em logs de fluxo ajudam a coletar dados para conformidade, auditoria e monitoramento do seu perfil de segurança de rede.

**Prática recomendada**: diagnosticar problemas de conectividade de VPN.  
**Detalhe**: use o Observador de Rede para [diagnosticar os problemas mais comuns de Gateway de VPN e conexão](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Você pode não apenas identificar o problema, como também usar logs detalhados para investigar ainda mais.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Implantação segura usando ferramentas do DevOps comprovadas
Use as seguintes práticas recomendadas de DevOps para garantir que suas equipes e sua empresa sejam produtivas e eficientes.

**Prática recomendada**: automatizar a compilação e a implantação de serviços.  
**Detalhe**: [infraestrutura como código](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) é um conjunto de técnicas e práticas recomendadas que ajudam os profissionais de TI a remover a sobrecarga de compilação e do gerenciamento diários da infraestrutura modular. Habilita os profissionais de TI a criar e realizar a manutenção do ambiente de servidor moderno de maneira semelhante a como os desenvolvedores de software criam e mantêm o código do aplicativo.

Você pode usar o [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) para provisionar seus aplicativos usando um modelo declarativo. Em um modelo único, você pode implantar vários serviços, juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo em cada estágio do ciclo de vida do aplicativo.

**Melhor prática**: compile e implante automaticamente serviços de nuvem ou aplicativos Web do Azure.  
**Detalhe**: Você pode configurar seus Projetos Azure DevOps para [criar e implantar automaticamente em](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) aplicativos web do Azure ou serviços em nuvem. O Azure DevOps implanta automaticamente os binários depois de fazer uma compilação no Azure após cada check-in de código. O processo de build do pacote é equivalente ao comando Package no Visual Studio, e as etapas de publicação equivalem ao comando Publish do Visual Studio.

**Melhor prática**: automatizar o gerenciamento de versão.  
**Detalhe:**[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) é uma solução para automatizar a implantação em vários estágios e gerenciar o processo de lançamento. Crie pipelines de implantação gerenciados e contínuos, a fim de lançar com rapidez, facilidade e frequência. Com o Azure Pipelines, você pode automatizar o processo de liberação e pode ter fluxos de trabalho de aprovação predefinidos. Implante localmente e na nuvem, estenda e personalize conforme a necessidade.

**Melhor prática**: verifique o desempenho de seu aplicativo antes de inicializá-lo ou implantar atualizações na produção.  
**Detalhe**: Execute testes de [carga baseados](/azure/devops/test/load-test/overview#alternatives) em nuvem para:

- Localizar problemas de desempenho em seu aplicativo Web.
- Melhorar a qualidade da implantação.
- Garantir que seu aplicativo esteja sempre disponível.
- Garantir que seu aplicativo possa lidar com o tráfego da sua próxima campanha de marketing ou de seu próximo lançamento.

[Apache JMeter](https://jmeter.apache.org/) é uma ferramenta de código aberto livre e popular com um forte apoio da comunidade.

**Melhor prática**: monitorar o desempenho do aplicativo.  
**Detalhe**: o [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço de APM (gerenciamento de desempenho de aplicativo) extensível para desenvolvedores da Web em várias plataformas. Use o Application Insights para monitorar seu aplicativo Web em tempo real. Ele detecta anomalias de desempenho automaticamente. Ele inclui ferramentas de análise para ajudar você a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo.

## <a name="mitigate-and-protect-against-ddos"></a>Atenuação de riscos e proteção contra DDoS
Ataque de DDoS (negação de serviço distribuído) é um tipo de ataque que tenta esgotar os recursos do aplicativo. A meta é afetar a disponibilidade do aplicativo e sua capacidade de lidar com solicitações legítimas. Esses ataques estão se tornando cada vez mais sofisticados e maiores tanto em termos de tamanho quanto de impacto. Eles podem ser direcionados a qualquer ponto de extremidade publicamente acessível pela Internet.

Projetar e criar para garantir a resiliência contra DDoS exige planejar e projetar para uma variedade de modos de falha. A seguir, estão as melhores práticas para a criação de serviços resilientes a DDoS no Azure.

**Melhor prática**: garanta que a segurança seja uma prioridade durante todo o ciclo de vida de um aplicativo, desde o design e implementação até a implantação e as operações. Os aplicativos podem ter bugs que permitam que um volume relativamente baixo de solicitações use muitos recursos, resultando em uma interrupção de serviço.  
**Detalhe**: para ajudar a proteger um serviço em execução no Microsoft Azure, você deve ter uma boa compreensão da arquitetura de seus aplicativos e se concentrar nos [Cinco pilares de qualidade de software](https://docs.microsoft.com/azure/architecture/guide/pillars). Você deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre o aplicativo e outros aplicativos e os pontos de extremidade de serviço expostos à Internet pública.

O mais importante é garantir que um aplicativo seja resiliente o suficiente para lidar com uma negação de serviço direcionados ao próprio aplicativo. A segurança e a privacidade estão incorporadas na plataforma do Azure, começando com o [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl). O SDL trata da segurança em cada fase do desenvolvimento e garante que o Azure seja atualizado continuamente para torná-lo ainda mais seguro.

**Melhor prática**: projete seus aplicativos para que sejam [escalados horizontalmente](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) para atender à demanda de uma carga amplificada, especificamente em caso de ataque de DDoS. Se seu aplicativo depender de uma única instância de um serviço, ele criará um único ponto de falha. O provisionamento de várias instâncias torna o sistema mais resiliente e mais escalonável.  
**Detalhe**: para o [Serviço de Aplicativo do Azure](/azure/app-service/app-service-value-prop-what-is), selecione um [Plano do Serviço de Aplicativo](../../app-service/overview-hosting-plans.md) que ofereça várias instâncias.

Para Serviços de Nuvem do Azure, configure cada uma das suas funções para usar [várias instâncias](../../cloud-services/cloud-services-choose-me.md).

Para [Máquinas Virtuais do Azure](/azure/virtual-machines/windows/overview), verifique se sua arquitetura de VM inclui mais de uma VM e se cada uma delas está incluída em um [conjunto de disponibilidade](/azure/virtual-machines/virtual-machines-windows-manage-availability). É recomendável usar conjuntos de dimensionamento de máquinas virtuais para obter recursos de dimensionamento automático.

**Melhor prática**: dispor as defesas de segurança em camadas em um aplicativo reduz a possibilidade de sucesso de um ataque. Implemente designs seguros para seus aplicativos ao utilizar recursos internos da plataforma Azure.  
**Detalhe**: por exemplo, o risco de ataque aumenta conforme o tamanho (área da superfície) do aplicativo. Você pode reduzir a área da superfície usando a lista de permissões para fechar o espaço de endereços IP exposto e portas de escuta que não são necessários em balanceadores de carga ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) e [Gateway de Aplicativo do Azure](/azure/application-gateway/application-gateway-create-probe-portal)).

[Grupos de segurança de rede](../../virtual-network/security-overview.md) são outra maneira de reduzir a superfície de ataque. Você pode usar [marcas de serviço](../../virtual-network/security-overview.md#service-tags) e [grupos de segurança de aplicativo](../../virtual-network/security-overview.md#application-security-groups) para minimizar a complexidade da criação de regras de segurança e a configuração da segurança de rede, como uma extensão natural da estrutura do aplicativo.

Você deve implantar os serviços do Azure em uma [rede virtual](../../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço se comuniquem por meio de endereços IP privados. O tráfego do serviço do Azure de uma rede virtual usa Endereços IP Públicos como endereços IP de origem por padrão.

Usar [pontos de extremidade de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md) altera o tráfego de serviço para usar endereços de rede virtual privados como endereços IP de origem ao acessar o serviço do Azure de uma rede virtual.

Muitos recursos locais dos clientes são atacados juntamente com seus recursos no Azure. Se você estiver conectando um ambiente local ao Azure, minimize a exposição dos recursos locais à Internet pública.

O Azure tem duas [ofertas de serviço](../../virtual-network/ddos-protection-overview.md) contra DDoS que fornecem proteção contra ataques de rede:

- A proteção básica é integrada à plataforma do Azure por padrão, sem custos adicionais. A escala e a capacidade da rede implantada globalmente do Azure fornecem defesa contra ataques de camada de rede comum por meio de monitoramento de tráfego sempre ativo e mitigação em tempo real. A básica não exige nenhuma alteração ao aplicativo ou à configuração do usuário e ajuda a proteger todos os serviços do Azure, incluindo serviços de PaaS, como o DNS do Azure.
- A proteção Standard fornece funcionalidades avançadas de atenuação de DDoS contra ataques de rede. Se ajusta automaticamente para proteger os recursos específicos do Azure. É muito simples habilitar a proteção durante a criação de redes virtuais. Isso também pode ser feito após a criação e não requer nenhuma alteração de aplicativo ou recurso.

## <a name="enable-azure-policy"></a>Habilitar a política do Azure
[A Azure Policy](/azure/governance/policy/overview) é um serviço no Azure que você usa para criar, atribuir e gerenciar políticas. Essas políticas impõem regras e efeitos sobre seus recursos, de modo que esses recursos permanecem em conformidade com seus padrões corporativos e contratos de nível de serviço. O Azure Policy atende a essa necessidade, avaliando os recursos quanto a não conformidade com políticas atribuídas.

Habilite a Política Do Azure para monitorar e aplicar a política escrita da sua organização. Isso garantirá a conformidade com os requisitos de segurança da sua empresa ou regulamentador, gerenciando centralmente as políticas de segurança em suas cargas de trabalho na nuvem híbrida. Aprenda a [criar e gerenciar políticas para impor o cumprimento.](../../governance/policy/tutorials/create-and-manage.md) Consulte [a estrutura de definição da Diretiva Azure](../../governance/policy/concepts/definition-structure.md) para obter uma visão geral dos elementos de uma política.

Aqui estão algumas práticas recomendadas de segurança a seguir depois de adotar a Política Do Azure:

**Melhores práticas**: A política suporta vários tipos de efeitos. Você pode ler sobre eles na [estrutura de definição da Política Do Azure](../../governance/policy/concepts/definition-structure.md#policy-rule). As operações comerciais podem ser afetadas negativamente pelo efeito **de negação** e pelo efeito **reparador,** então comece com o efeito **de auditoria** para limitar o risco de impacto negativo da política.   
**Detalhe**: [Inicie implantações de políticas no modo de auditoria](../../governance/policy/concepts/definition-structure.md#policy-rule) e, posteriormente, progrida para **negar** ou **remediar**. Teste e revise os resultados do efeito de auditoria antes de se mover para **negar** ou **remediar**.

Para obter mais informações, consulte [Criar e gerenciar políticas para impor o cumprimento](../../governance/policy/tutorials/create-and-manage.md).

**Melhores práticas**: Identificar os papéis responsáveis pelo monitoramento das violações das políticas e garantir que as ações de remediação corretas sejam tomadas rapidamente.   
**Detalhe**: Tenha a função atribuída monitorar a conformidade através do [portal Azure](../../governance/policy/how-to/get-compliance-data.md#portal) ou através da [linha de comando](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Melhores práticas**: A Azure Policy é uma representação técnica das políticas escritas de uma organização. Mapeie todas as políticas do Azure para políticas organizacionais para reduzir a confusão e aumentar a consistência.   
**Detalhe**: Mapeamento de documentos na documentação da sua organização ou na própria política do Azure, adicionando uma referência à política organizacional na descrição da [política](../../governance/policy/concepts/definition-structure.md#display-name-and-description) do Azure ou na descrição da [iniciativa](../../governance/policy/concepts/definition-structure.md#initiatives) de política do Azure.

## <a name="monitor-azure-ad-risk-reports"></a>Monitore os relatórios de risco do Azure AD
A grande maioria das violações de segurança ocorre quando os invasores conseguem acessar a um ambiente roubando a identidade de um usuário. Descobrir identidades comprometidas não é uma tarefa fácil. O Azure AD usa algoritmos de aprendizado de máquina e heurística adaptáveis para detectar ações suspeitas relacionadas às contas do usuário. Cada ação suspeita detectada é armazenada em um registro chamado [dedetecção de risco](../../active-directory/reports-monitoring/concept-risk-events.md). As detecções de risco são registradas nos relatórios de segurança do Azure AD. Para obter mais informações, leia sobre o relatório de [segurança de riscos](../../active-directory/reports-monitoring/concept-user-at-risk.md) dos usuários e o relatório de segurança de [logins de risco](../../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Próximas etapas
Veja [Melhores práticas e padrões de segurança do Azure](best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.

Os seguintes recursos estão disponíveis para fornecer mais informações gerais sobre a segurança do Azure e os serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as últimas novidades de Segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - onde as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou por e-mail parasecure@microsoft.com
