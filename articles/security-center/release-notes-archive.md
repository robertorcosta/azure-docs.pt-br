---
title: Arquivo de novidades na central de segurança do Azure
description: Uma descrição do que há de novo e mudou na central de segurança do Azure de seis meses atrás e antes.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: c0883e91d5e806fb166c3ddeafc4ce130ff3f66f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210837"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Arquivo morto para o que há de novo na central de segurança do Azure?

As novidades principais do [Azure Active Directory?](release-notes.md) a página notas de versão contém atualizações dos últimos seis meses, enquanto essa página contém itens mais antigos.

Esta página fornece informações sobre:

- Novos recursos
- Correções de bug
- Funcionalidades preteridas

## <a name="november-2019"></a>Novembro de 2019

### <a name="threat-protection-for-azure-key-vault-in-public-preview-in-north-america-regions"></a>Proteção contra ameaças para Azure Key Vault em visualização pública em regiões América do Norte

Azure Key Vault é um serviço essencial para proteger dados e melhorar o desempenho de aplicativos em nuvem, oferecendo a capacidade de gerenciar centralmente chaves, segredos, chaves de criptografia e políticas na nuvem. Como o Azure Key Vault armazena dados confidenciais e críticos para os negócios, ele requer segurança máxima para os cofres de chaves e os dados armazenados neles.

O suporte da central de segurança do Azure para proteção contra ameaças para Azure Key Vault fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar cofres de chaves. Essa nova camada de proteção permite que os clientes resolvam ameaças contra seus cofres de chaves sem ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança. O recurso está em visualização pública em regiões de América do Norte.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Proteção contra ameaças para o armazenamento do Azure inclui triagem de reputação de malware

A proteção contra ameaças para o armazenamento do Azure oferece novas detecções alimentadas pela inteligência contra ameaças da Microsoft para detectar carregamentos de malware no armazenamento do Azure usando análise de reputação de hash e acesso suspeito de um nó de saída de Tor ativo (um proxy de anonimato). Agora você pode exibir o malware detectado em contas de armazenamento usando a central de segurança do Azure.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automação de fluxo de trabalho com aplicativos lógicos (visualização)

As organizações com segurança gerenciada centralmente e ti/operações implementam processos de fluxo de trabalho internos para impulsionar a ação necessária na organização quando as discrepâncias são descobertas em seus ambientes. Em muitos casos, esses fluxos de trabalho são processos repetíveis e a automação pode simplificar muito os processos na organização.

Hoje, estamos introduzindo um novo recurso na central de segurança que permite que os clientes criem configurações de automação aproveitando os aplicativos lógicos do Azure e criem políticas que irão dispará-los automaticamente com base em conclusões de ASC específicas, como recomendações ou alertas. O aplicativo lógico do Azure pode ser configurado para fazer qualquer ação personalizada com suporte pela vasta comunidade de conectores de aplicativos lógicos ou usar um dos modelos fornecidos pela central de segurança, como enviar um email ou abrir um tíquete de™ do ServiceNow.

Para obter mais informações sobre os recursos de central de segurança automática e manual para executar seus fluxos de trabalho, consulte [automação de fluxo de trabalho](workflow-automation.md).

Para saber mais sobre a criação de aplicativos lógicos, consulte [aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Correção rápida para recursos em massa disponíveis para o público geral

Com as muitas tarefas que um usuário recebe como parte da Pontuação segura, a capacidade de corrigir efetivamente os problemas em um grande frota pode se tornar desafiador.

Para simplificar a correção de configurações incorretas de segurança e ser capaz de corrigir rapidamente as recomendações em uma grande quantidade de recursos e melhorar sua pontuação segura, use a correção rápida de correção.

Esta operação permitirá selecionar os recursos para os quais você deseja aplicar a correção e iniciar uma ação de correção que irá definir a configuração em seu nome.

A correção rápida geralmente está disponível para clientes atuais como parte da página de recomendações da central de segurança.

Veja quais recomendações têm correção rápida habilitada no [Guia de referência para recomendações de segurança](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Verificar se há vulnerabilidades em imagens de contêiner (visualização)

A central de segurança do Azure agora pode verificar as imagens de contêiner no registro de contêiner do Azure em busca de vulnerabilidades.

A verificação de imagem funciona analisando o arquivo de imagem de contêiner e, em seguida, verificando se há alguma vulnerabilidade conhecida (da plataforma Qualys).

A varredura em si é disparada automaticamente ao enviar por push novas imagens de contêiner para o registro de contêiner do Azure. As vulnerabilidades encontradas surgirão como recomendações da central de segurança e incluídas na pontuação de segurança do Azure, juntamente com informações sobre como corrigi-las para reduzir a superfície de ataque permitida.


### <a name="additional-regulatory-compliance-standards-preview"></a>Padrões adicionais de conformidade regulatória (versão prévia)

O painel de conformidade regulatória fornece informações sobre sua postura de conformidade com base nas avaliações da central de segurança. O painel mostra como o seu ambiente está em conformidade com os controles e requisitos designados por padrões regulatórios específicos e benchmarks do setor e fornece recomendações prescritivas sobre como lidar com esses requisitos.

O painel de conformidade regulatória tem, até o momento, quatro padrões internos: Azure CIS 1.1.0, PCI-DSS, ISO 27001 e SOC-TSP. Agora estamos anunciando a versão de visualização pública de padrões adicionais com suporte: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canadá Federal PBMM e Reino Unido, em conjunto com NHS do Reino Unido. Também estamos lançando uma versão atualizada do Azure CIS 1.1.0, cobrindo mais controles do padrão e aprimorando a extensibilidade.

[Saiba mais sobre como personalizar o conjunto de padrões em seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Proteção contra ameaças para o serviço kubernetes do Azure (versão prévia)

O kubernetes está se tornando rapidamente o novo padrão para implantar e gerenciar software na nuvem. Poucas pessoas têm ampla experiência com kubernetes e muitos se concentram apenas em engenharia e administração geral e ignoram o aspecto de segurança. O ambiente kubernetes precisa ser configurado com cuidado para ser seguro, garantindo que nenhuma das portas da superfície de ataque focadas no contêiner não seja deixada aberta é exposta para invasores. A central de segurança está expandindo seu suporte no espaço do contêiner para um dos serviços de crescimento mais rápido no Azure – AKS (serviço kubernetes do Azure).

Os novos recursos desta versão de visualização pública incluem:

- **Descoberta & visibilidade** – descoberta contínua de instâncias AKs gerenciadas nas assinaturas registradas da central de segurança.
- **Recomendações de Pontuação segura** -itens acionáveis para ajudar os clientes a cumprir as práticas recomendadas de segurança no AKs como parte da Pontuação segura do cliente, como "o controle de acesso baseado em função deve ser usado para restringir o acesso a um cluster do serviço kubernetes".
- **Detecção de ameaças** -análise baseada em host e cluster, como "um contêiner privilegiado detectado".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Avaliação de vulnerabilidade de máquina virtual (versão prévia)

Os aplicativos instalados em máquinas virtuais geralmente podem ter vulnerabilidades que poderiam levar a uma violação da máquina virtual. Estamos anunciando que a camada Standard da central de segurança inclui a avaliação de vulnerabilidades interna para máquinas virtuais sem nenhuma taxa adicional. A avaliação de vulnerabilidade, fornecida pela Qualys na visualização pública, permitirá que você examine continuamente todos os aplicativos instalados em uma máquina virtual para encontrar aplicativos vulneráveis e apresente as descobertas na experiência do portal da central de segurança. A central de segurança cuida de todas as operações de implantação para que não seja necessário nenhum trabalho extra do usuário. No futuro, estamos planejando fornecer opções de avaliação de vulnerabilidade para dar suporte às necessidades de negócios exclusivas de nossos clientes.

[Saiba mais sobre as avaliações de vulnerabilidade para suas máquinas virtuais do Azure](security-center-vulnerability-assessment-recommendations.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Segurança de dados avançada para servidores SQL em máquinas virtuais do Azure (versão prévia)

O suporte da central de segurança do Azure para proteção contra ameaças e avaliação de vulnerabilidade para bancos de SQL em execução em VMs IaaS agora está em versão prévia.

A [avaliação de vulnerabilidades](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) é um serviço fácil de configurar que pode descobrir, acompanhar e ajudá-lo a corrigir possíveis vulnerabilidades no banco de dados. Ele fornece visibilidade da sua postura de segurança como parte da Pontuação segura do Azure e inclui as etapas para resolver problemas de segurança e aprimorar o fortifications do banco de dados.

A [proteção avançada contra ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar o SQL Server. Ele monitora continuamente seu banco de dados em busca de atividades suspeitas e fornece alertas de segurança orientados a ações em padrões de acesso de banco de dados anormais. Esses alertas fornecem os detalhes da atividade suspeita e as ações recomendadas para investigar e atenuar a ameaça.


### <a name="support-for-custom-policies-preview"></a>Suporte para políticas personalizadas (versão prévia)

A central de segurança do Azure agora dá suporte a políticas personalizadas (em versão prévia).

Nossos clientes têm a desejar estender sua cobertura atual de avaliações de segurança na central de segurança com suas próprias avaliações de segurança com base nas políticas que elas criam em Azure Policy. Com suporte para políticas personalizadas, isso agora é possível.

Essas novas políticas serão parte da experiência de recomendações da central de segurança, da Pontuação segura e do painel de padrões de conformidade regulatória. Com o suporte para políticas personalizadas, agora você pode criar uma iniciativa personalizada no Azure Policy e, em seguida, adicioná-la como uma política na central de segurança e visualizá-la como uma recomendação.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Estendendo a cobertura da central de segurança do Azure com plataforma para comunidade e parceiros

Use a central de segurança para receber recomendações não apenas da Microsoft, mas também de soluções existentes de parceiros como Check Point, tenable e CyberArk com muito mais integrações chegando.  O fluxo de integração simples da central de segurança pode conectar suas soluções existentes à central de segurança, permitindo que você exiba suas recomendações de postura de segurança em um único lugar, execute relatórios unificados e aproveite todos os recursos da central de segurança em relação às recomendações internas e de parceiros. Você também pode exportar recomendações da central de segurança para produtos de parceiros.

[Saiba mais sobre a associação de segurança inteligente da Microsoft](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Integrações avançadas com a exportação de recomendações e alertas (versão prévia)

Para habilitar cenários de nível empresarial na parte superior da central de segurança, agora é possível consumir alertas e recomendações da central de segurança em locais adicionais, exceto o portal do Azure ou a API. Eles podem ser exportados diretamente para um hub de eventos e para Log Analytics espaços de trabalho. Aqui estão alguns fluxos de trabalho que você pode criar com base nesses novos recursos:

- Com exportar para Log Analytics espaço de trabalho, você pode criar painéis personalizados com Power BI.
- Com a exportação para o Hub de eventos, você poderá exportar alertas e recomendações da central de segurança para seus SIEMs de terceiros, para uma solução de terceiros em tempo real ou Data Explorer do Azure.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Carregar servidores locais na central de segurança do centro de administração do Windows (versão prévia)

O centro de administração do Windows é um portal de gerenciamento para servidores Windows que não estão implantados no Azure, oferecendo vários recursos de gerenciamento do Azure, como atualizações de sistema e backup. Recentemente, adicionamos uma capacidade de carregar esses servidores não Azure para serem protegidos pelo ASC diretamente da experiência do centro de administração do Windows.

Com essa nova experiência, os usuários serão integrar um servidor WAC à central de segurança do Azure e habilitar a exibição de seus alertas de segurança e recomendações diretamente na experiência do centro de administração do Windows.


## <a name="september-2019"></a>Setembro de 2019

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Gerenciando regras com aprimoramentos de controles de aplicativos adaptáveis

A experiência de gerenciar regras para máquinas virtuais usando controles de aplicativos adaptáveis foi aprimorada. Os controles de aplicativo adaptáveis da central de segurança do Azure ajudam a controlar quais aplicativos podem ser executados em suas máquinas virtuais. Além de uma melhoria geral no gerenciamento de regras, um novo benefício permite que você controle quais tipos de arquivo serão protegidos quando você adicionar uma nova regra.

[Saiba mais sobre controles de aplicativo adaptáveis](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Controlar a recomendação de segurança do contêiner usando Azure Policy

A recomendação da central de segurança do Azure para corrigir vulnerabilidades em segurança de contêiner agora pode ser habilitada ou desabilitada via Azure Policy.

Para exibir as políticas de segurança habilitadas, na central de segurança, abra a página política de segurança.


## <a name="august-2019"></a>Agosto de 2019

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Acesso à VM just-in-time (JIT) para o Firewall do Azure 

O acesso à VM just-in-time (JIT) para o Firewall do Azure já está disponível para o público geral. Use-o para proteger seus ambientes protegidos por firewall do Azure, além de seus ambientes protegidos por NSG.

O acesso à VM JIT reduz a exposição a ataques de volumétricos de rede, fornecendo acesso controlado às VMs somente quando necessário, usando suas regras de NSG e firewall do Azure.

Quando você habilita o JIT para suas VMs, cria uma política que determina as portas a serem protegidas, por quanto tempo as portas devem permanecer abertas e os endereços IP aprovados de onde essas portas podem ser acessadas. Essa política ajuda você a manter o controle do que os usuários podem fazer quando solicitam acesso.

As solicitações são registradas no log de atividades do Azure, para que você possa monitorar e auditar facilmente o acesso. A página just-in-time também ajuda você a identificar rapidamente as VMs existentes que têm o JIT habilitado e as VMs em que o JIT é recomendado.

[Saiba mais sobre o Firewall do Azure](https://docs.microsoft.com/azure/firewall/overview).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Correção de clique único para impulsionar sua postura de segurança (versão prévia)

A pontuação segura é uma ferramenta que ajuda a avaliar sua postura de segurança de carga de trabalho. Ele revisa suas recomendações de segurança e as prioriza para você, para que você saiba quais recomendações executar primeiro. Isso ajuda a encontrar as vulnerabilidades de segurança mais sérias para priorizar a investigação.

Para simplificar a correção de configurações incorretas de segurança e ajudá-lo a melhorar rapidamente sua pontuação segura, adicionamos um novo recurso que permite que você corrija uma recomendação em uma grande quantidade de recursos em um único clique.

Esta operação permitirá selecionar os recursos para os quais você deseja aplicar a correção e iniciar uma ação de correção que irá definir a configuração em seu nome.

Veja quais recomendações têm correção rápida habilitada no [Guia de referência para recomendações de segurança](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Gerenciamento entre locatários

A central de segurança agora dá suporte a cenários de gerenciamento entre locatários como parte do Azure Lighthouse. Isso permite que você tenha visibilidade e gerencie a postura de segurança de vários locatários na central de segurança. 

[Saiba mais sobre as experiências de gerenciamento entre locatários](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Julho de 2019

### <a name="updates-to-network-recommendations"></a>Atualizações para recomendações de rede

A central de segurança do Azure (ASC) lançou novas recomendações de rede e melhorou algumas delas. Agora, o uso da central de segurança garante uma proteção de rede ainda maior para seus recursos. 

[Saiba mais sobre as recomendações de rede](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>Junho de 2019

### <a name="adaptive-network-hardening---generally-available"></a>Proteção de rede adaptável – disponível para o público geral

Uma das maiores superfícies de ataque para cargas de trabalho em execução na nuvem pública são conexões de e para a Internet pública. Nossos clientes acham difícil saber quais regras do NSG (grupo de segurança de rede) devem estar em vigor para garantir que as cargas de trabalho do Azure estejam disponíveis apenas para os intervalos de origem necessários. Com esse recurso, a central de segurança aprende os padrões de tráfego e conectividade de rede das cargas de trabalho do Azure e fornece recomendações de regras de NSG para máquinas virtuais voltadas para a Internet. Isso ajuda o cliente a configurar melhor suas políticas de acesso à rede e limitar sua exposição a ataques. 

[Saiba mais sobre a proteção de rede adaptável](security-center-adaptive-network-hardening.md).
