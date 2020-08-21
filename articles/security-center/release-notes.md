---
title: Notas sobre a versão da Central de Segurança do Azure
description: Uma descrição do que há de novo e do que mudou na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: memildin
ms.openlocfilehash: c7df035aec199953bdf9a6bd56262af70a5a77e7
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723955"
---
# <a name="whats-new-in-azure-security-center"></a>Novidades na Central de Segurança do Azure

A segurança do Azure está em desenvolvimento ativo e recebe aprimoramentos continuamente. Para se manter atualizado com os desenvolvimentos mais recentes, esta página fornece a você informações sobre:

- Novos recursos
- Correções de bug
- Funcionalidades preteridas

Esta página é atualizada regularmente, então visite-a com frequência. Se você estiver procurando itens que têm mais de seis meses, poderá encontrá-los nos [Arquivos de O que há de novo na Central de Segurança do Azure](release-notes-archive.md).


## <a name="august-2020"></a>Agosto de 2020

As atualizações em agosto incluem:

- [Inventário de ativos-nova exibição avançada da postura de segurança de seus ativos](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Suporte adicionado para padrões de segurança de Azure Active Directory (para autenticação multifator)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Recomendação de entidades de serviço adicionada](#service-principals-recommendation-added)
- [Avaliação de vulnerabilidade em VMs – recomendações e políticas consolidadas](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Novas políticas de segurança AKS adicionadas à iniciativa de ASC_default – para uso somente por clientes de visualização privada](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventário de ativos-nova exibição avançada da postura de segurança de seus ativos

O inventário de ativos da central de segurança (atualmente em visualização) fornece uma maneira de exibir a postura de segurança dos recursos que você conectou à central de segurança.

A central de segurança analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, ele fornece recomendações sobre como corrigir essas vulnerabilidades. Quando qualquer recurso tiver recomendações pendentes, eles aparecerão no inventário.

Você pode usar a exibição e seus filtros para explorar seus dados de postura de segurança e realizar ações adicionais com base em suas descobertas.

Saiba mais sobre o [inventário de ativos](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Suporte adicionado para padrões de segurança de Azure Active Directory (para autenticação multifator)

A central de segurança adicionou suporte completo para [os padrões de segurança](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), as proteções de segurança de identidade gratuita da Microsoft.

Os padrões de segurança fornecem configurações de segurança de identidade pré-configuradas para defender sua organização contra ataques comuns relacionados à identidade. Os padrões de segurança já estão protegendo mais de 5 milhões locatários em geral; os locatários 50.000 também são protegidos pela central de segurança.

A central de segurança agora fornece uma recomendação de segurança sempre que identifica uma assinatura do Azure sem os padrões de segurança habilitados. Até agora, a central de segurança recomenda habilitar a autenticação multifator usando o acesso condicional, que faz parte da licença Premium do Azure Active Directory (AD). Para clientes que usam o Azure AD gratuito, agora é recomendável habilitar os padrões de segurança. 

Nossa meta é encorajar mais clientes a proteger seus ambientes de nuvem com o MFA e reduzir um dos maiores riscos que também é o mais impactante em sua [Pontuação segura](https://docs.microsoft.com/azure/security-center/secure-score-security-controls).

Saiba mais sobre [os padrões de segurança](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults).


### <a name="service-principals-recommendation-added"></a>Recomendação de entidades de serviço adicionada

Uma nova recomendação foi adicionada para recomendar que os clientes da central de segurança que usam certificados de gerenciamento para gerenciar suas assinaturas alternem para entidades de serviço.

A recomendação, as **entidades de serviço devem ser usadas para proteger suas assinaturas em vez de certificados de gerenciamento,** aconselham você a usar entidades de serviço ou Azure Resource Manager para gerenciar com mais segurança suas assinaturas. 

Saiba mais sobre [objetos de aplicativo e entidade de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Avaliação de vulnerabilidade em VMs – recomendações e políticas consolidadas

A central de segurança inspeciona suas VMs para detectar se estão executando uma solução de avaliação de vulnerabilidade. Se nenhuma solução de avaliação de vulnerabilidade for encontrada, a central de segurança fornecerá uma recomendação para simplificar a implantação.

Quando as vulnerabilidades são encontradas, a central de segurança fornece uma recomendação Resumindo as descobertas para você investigar e corrigir conforme necessário.

Para garantir uma experiência consistente para todos os usuários, independentemente do tipo de verificador que estão usando, nós unificamos quatro recomendações nos seguintes dois:

|Recomendação unificada|Descrição da alteração|
|----|:----|
|**Uma solução de avaliação de vulnerabilidade deve ser habilitada em suas máquinas virtuais**|O substitui as duas seguintes recomendações:<br> **•** Habilitar a solução de avaliação de vulnerabilidades interna em máquinas virtuais (da plataforma Qualys (agora preterido) (incluído com a camada Standard)<br> **•** A solução de avaliação de vulnerabilidade deve ser instalada em suas máquinas virtuais (agora preteridas) (camadas padrão e gratuita)|
|**Vulnerabilidades em suas máquinas virtuais devem ser corrigidas**|O substitui as duas seguintes recomendações:<br>**•** Corrigir vulnerabilidades encontradas em suas máquinas virtuais (de plataforma Qualys) (agora preteridas)<br>**•** As vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade (agora preterida)|
|||

Agora você usará a mesma recomendação para implantar a extensão de avaliação de vulnerabilidade da central de segurança ou uma solução de licença privada ("BYOL") de um parceiro, como Qualys ou Rapid7.

Além disso, quando as vulnerabilidades forem encontradas e relatadas à central de segurança, uma única recomendação irá alertá-lo sobre as descobertas, independentemente da solução de avaliação de vulnerabilidades que as identificou.

#### <a name="updating-dependencies"></a>Atualizando dependências

Se você tiver scripts, consultas ou automaçãos referentes às recomendações ou aos nomes/chaves de política anteriores, use as tabelas abaixo para atualizar as referências:

##### <a name="before-august-2020"></a>Antes de agosto de 2020

|Recomendação|Escopo|
|----|:----|
|**Habilitar a solução de avaliação de vulnerabilidades interna em máquinas virtuais (da plataforma Qualys)**<br>Chave: 550e890b-e652-4D22-8274-60b3bdb24c63|Interno|
|**Corrija as vulnerabilidades encontradas em suas máquinas virtuais (da plataforma Qualys)**<br>Chave: 1195afff-c881-495e-9bc5-1486211ae03f|Interno|
|**A solução de avaliação de vulnerabilidades deve ser instalada nas suas máquinas virtuais**<br>Chave: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades**<br>Chave: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Política|Escopo|
|----|:----|
|**A avaliação de vulnerabilidades deve estar habilitada nas máquinas virtuais**<br>ID da política: 501541f7-f7e7-4cd6-868C-4190fdad3ac9|Interno|
|**Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade**<br>ID da política: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>De agosto de 2020

|Recomendação|Escopo|
|----|:----|
|**Uma solução de avaliação de vulnerabilidade deve ser habilitada em suas máquinas virtuais**<br>Chave: ffff0522-1e88-47fc-8382-2a80ba848f5d|Interno + BYOL|
|**Vulnerabilidades em suas máquinas virtuais devem ser corrigidas**<br>Chave: 1195afff-c881-495e-9bc5-1486211ae03f|Interno + BYOL|
||||

|Política|Escopo|
|----|:----|
|[**A avaliação de vulnerabilidade deve ser habilitada em máquinas virtuais**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID da política: 501541f7-f7e7-4cd6-868C-4190fdad3ac9 |Interno + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Novas políticas de segurança AKS adicionadas à iniciativa de ASC_default – para uso somente por clientes de visualização privada

Para garantir que as cargas de trabalho do kubernetes sejam seguras por padrão, a central de segurança está adicionando políticas de nível de kubernetes e recomendações de proteção, incluindo opções de imposição com o controle de admissão kubernetes.

A fase inicial deste projeto inclui uma visualização privada e a adição de políticas novas (desabilitadas por padrão) à iniciativa de ASC_default.

Você pode ignorar essas políticas com segurança e não haverá nenhum impacto no seu ambiente. Se você quiser habilitá-los, Inscreva-se para a visualização em https://aka.ms/SecurityPrP e selecione uma das seguintes opções:

1. **Visualização única** – para unir somente esta visualização privada. Mencione explicitamente "verificação contínua ASC" como a visualização que você gostaria de unir.
1. **Programa em andamento** – a ser adicionado a esta e a visualizações privadas futuras. Será necessário concluir um perfil e um contrato de privacidade.


## <a name="july-2020"></a>Julho de 2020

As atualizações em julho incluem:
- [A avaliação de vulnerabilidade para máquinas virtuais agora está disponível para imagens que não são do Marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Proteção contra ameaças para o armazenamento do Azure expandida para incluir arquivos e Azure Data Lake Storage Gen2 do Azure (versão prévia)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Oito novas recomendações para habilitar os recursos de proteção contra ameaças](#eight-new-recommendations-to-enable-threat-protection-features)
- [Melhorias de segurança do contêiner-verificação mais rápida e documentação atualizada](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Controles de aplicativo adaptáveis atualizados com uma nova recomendação e suporte para curingas em regras de caminho](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Seis políticas para o SQL Advanced Data Security preteridas](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>A avaliação de vulnerabilidade para máquinas virtuais agora está disponível para imagens que não são do Marketplace

Ao implantar uma solução de avaliação de vulnerabilidade, a central de segurança realizou anteriormente uma verificação de validação antes da implantação. A verificação foi confirmar uma SKU do Marketplace da máquina virtual de destino. 

A partir dessa atualização, a verificação foi removida e agora você pode implantar ferramentas de avaliação de vulnerabilidade em computadores Windows e Linux "personalizados". As imagens personalizadas são aquelas que você modificou dos padrões do Marketplace.

Embora você possa implantar a extensão de avaliação de vulnerabilidade integrada (da plataforma de Qualys) em muitos outros computadores, o suporte estará disponível apenas se você estiver usando um sistema operacional listado em [implantando o scanner de vulnerabilidade interno do Qualys](built-in-vulnerability-assessment.md#deploying-the-qualys-built-in-vulnerability-scanner).

Saiba mais sobre o [Verificador de vulnerabilidades integrada para máquinas virtuais (somente na camada Standard)](built-in-vulnerability-assessment.md).

Saiba mais sobre como usar sua própria solução de avaliação de vulnerabilidade de licença privada do Qualys ou do Rapid7 em [implantando uma solução de verificação de vulnerabilidade de parceiro](partner-vulnerability-assessment.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Proteção contra ameaças para o armazenamento do Azure expandida para incluir arquivos e Azure Data Lake Storage Gen2 do Azure (versão prévia)

A proteção contra ameaças para o armazenamento do Azure detecta atividade potencialmente prejudicial em suas contas de armazenamento do Azure. A central de segurança exibe alertas quando detecta tentativas de acessar ou explorar suas contas de armazenamento. 

Seus dados podem ser protegidos independentemente de serem armazenados como contêineres de BLOB, compartilhamentos de arquivos ou data lagos. 

Saiba mais sobre [a proteção contra ameaças para o armazenamento do Azure](threat-protection.md#threat-protection-for-azure-storage-).




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Oito novas recomendações para habilitar os recursos de proteção contra ameaças

Oito novas recomendações foram adicionadas para fornecer uma maneira simples de habilitar os recursos de proteção contra ameaças da central de segurança do Azure para os seguintes tipos de recursos: máquinas virtuais, planos de serviço de aplicativo, servidores de banco de dados SQL do Azure, SQL Servers em máquinas, contas de armazenamento do Azure, clusters do serviço de contêiner do Azure, registros do registro de Container do Azure Azure Key Vault e cofre

As novas recomendações são:

- **A Segurança de Dados Avançada deve ser habilitada nos servidores do Banco de Dados SQL do Azure**
- **A Segurança de Dados Avançada deve ser habilitada nos servidores SQL em computadores**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos planos do Serviço de Aplicativo do Azure**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos registros do Registro de Contêiner do Azure**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos cofres do Azure Key Vault**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos clusters do Serviço de Kubernetes do Azure**
- **A Proteção Avançada contra Ameaças deve ser habilitada em contas de Armazenamento do Azure**
- **A proteção avançada contra ameaças deve ser habilitada em máquinas virtuais**

Essas novas recomendações pertencem ao controle de segurança **habilitar proteção avançada contra ameaças** .

As recomendações também incluem a capacidade de correção rápida. 

> [!IMPORTANT]
> Corrigir qualquer uma dessas recomendações resultará em encargos para proteger os recursos relevantes. Esses encargos serão iniciados imediatamente se você tiver recursos relacionados na assinatura atual. Ou no futuro, se você adicioná-los em uma data posterior.
> 
> Por exemplo, se você não tiver nenhum cluster do serviço kubernetes do Azure em sua assinatura e habilitar a proteção contra ameaças, nenhum encargo será cobrado. Se, no futuro, você adicionar um cluster na mesma assinatura, ele será automaticamente protegido e os encargos serão iniciados nesse momento.

Saiba mais sobre cada um deles na [página de referência de recomendações de segurança](recommendations-reference.md).

Saiba mais sobre a [proteção contra ameaças na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/threat-protection).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Melhorias de segurança do contêiner-verificação mais rápida e documentação atualizada

Como parte dos investimentos contínuos no domínio de segurança do contêiner, estamos felizes em compartilhar uma melhoria significativa de desempenho nas verificações dinâmicas da central de segurança de imagens de contêiner armazenadas no registro de contêiner do Azure. Agora, as verificações normalmente são concluídas em aproximadamente dois minutos. Em alguns casos, eles podem levar até 15 minutos.

Para melhorar a clareza e a orientação sobre os recursos de segurança do contêiner da central de segurança do Azure, também atualizamos as páginas de documentação de segurança do contêiner. 

Saiba mais sobre a segurança de contêiner da central de segurança nos seguintes artigos:

- [Visão geral dos recursos de segurança do contêiner da central de segurança](https://docs.microsoft.com/azure/security-center/container-security)
- [Detalhes da integração com o registro de contêiner do Azure](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)
- [Detalhes da integração com o serviço kubernetes do Azure](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)
- [Como verificar seus registros e proteger seus hosts do Docker](https://docs.microsoft.com/azure/security-center/monitor-container-security)
- [Alertas de segurança dos recursos de proteção contra ameaças para clusters do serviço kubernetes do Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)
- [Alertas de segurança dos recursos de proteção contra ameaças para hosts do serviço kubernetes do Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)
- [Recomendações de segurança para contêineres](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Controles de aplicativo adaptáveis atualizados com uma nova recomendação e suporte para curingas em regras de caminho

O recurso de controles de aplicativo adaptáveis recebeu duas atualizações significativas:

* Uma nova recomendação identifica o comportamento potencialmente legítimo que não era permitido anteriormente. A nova recomendação, **as regras de permissão daList em sua política de controle de aplicativo adaptável devem ser atualizadas**, solicita que você adicione novas regras à política existente para reduzir o número de falsos positivos em alertas de violação de controles de aplicativo adaptáveis.

* Agora, as regras de caminho dão suporte a curingas. A partir dessa atualização, você pode configurar as regras de caminho permitidas usando curingas. Há dois cenários com suporte:

    * Usando um curinga no final de um caminho para permitir todos os executáveis dentro desta pasta e subpastas

    * Usando um caractere curinga no meio de um caminho para habilitar um nome executável conhecido com um nome de pasta de alteração (por exemplo, pastas de usuário pessoais com um executável conhecido, nomes de pastas gerados automaticamente, etc.).


[Saiba mais sobre controles de aplicativo adaptáveis](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Seis políticas para o SQL Advanced Data Security preteridas

Seis políticas relacionadas à segurança de dados avançada para máquinas SQL estão sendo preteridas:

- Os tipos de proteção avançada contra ameaças devem ser definidos como ' todos ' na instância gerenciada do SQL configurações de segurança de dados avançadas
- Os tipos de proteção avançada contra ameaças devem ser definidos como ' todos ' nas configurações de segurança de dados avançadas do SQL Server
- As configurações da Segurança de Dados Avançada para a instância gerenciada do SQL devem conter um endereço de email para receber alertas de segurança
- As configurações da Segurança de Dados Avançada para o SQL Server devem conter um endereço de email para receber alertas de segurança
- As notificações por email para administradores e proprietários de assinatura devem ser habilitadas nas configurações da Segurança de Dados Avançada da instância gerenciada do SQL
- As notificações por email para os administradores e proprietários de assinaturas devem ser habilitadas nas configurações da Segurança de Dados Avançada do SQL Server

Saiba mais sobre [as políticas internas](security-center-policy-definitions.md).





## <a name="june-2020"></a>Junho de 2020

As atualizações em junho incluem:
- [API de Pontuação segura (visualização)](#secure-score-api-preview)
- [Segurança de dados avançada para máquinas SQL (Azure, outras nuvens e local) (visualização)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Duas novas recomendações para implantar o agente de Log Analytics em máquinas de Arc do Azure (versão prévia)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Novas políticas para criar configurações contínuas de exportação e automação de fluxo de trabalho em escala](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nova recomendação para usar o NSGs para proteger máquinas virtuais não voltadas para a Internet](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Novas políticas para habilitar a proteção contra ameaças e a segurança de dados avançada](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>API de Pontuação segura (visualização)

Agora você pode acessar sua pontuação por meio da [API de Pontuação segura](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (atualmente em visualização). Os métodos de API fornecem a flexibilidade para consultar os dados e criar seu próprio mecanismo de relatório de suas pontuações seguras ao longo do tempo. Por exemplo, você pode usar a API de **pontuações seguras** para obter a pontuação de uma assinatura específica. Além disso, você pode usar a API de **controles de Pontuação segura** para listar os controles de segurança e a pontuação atual de suas assinaturas.

Para obter exemplos de ferramentas externas possibilitadas com a API de Pontuação segura, consulte [a área de Pontuação segura de nossa comunidade do GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Saiba mais sobre a [Pontuação segura e os controles de segurança na central de segurança do Azure](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Segurança de dados avançada para máquinas SQL (Azure, outras nuvens e local) (visualização)

A segurança de dados avançada da central de segurança do Azure para máquinas SQL agora protege os SQL Servers hospedados no Azure, em outros ambientes de nuvem e até mesmo em computadores locais. Isso estende as proteções para seus SQL Servers nativos do Azure para oferecer suporte total a ambientes híbridos.

A segurança de dados avançada fornece avaliação de vulnerabilidade e proteção avançada contra ameaças para suas máquinas do SQL onde quer que estejam localizadas.

A instalação envolve duas etapas:

1. Implantar o agente de Log Analytics no computador host do SQL Server para fornecer a conexão à conta do Azure.

1. Habilitando o pacote opcional na página de preços e configurações da central de segurança.

Saiba mais sobre [a segurança de dados avançada para máquinas SQL](security-center-iaas-advanced-data.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Duas novas recomendações para implantar o agente de Log Analytics em máquinas de Arc do Azure (versão prévia)

Duas novas recomendações foram adicionadas para ajudar a implantar o [agente de log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) em seus computadores do Arc do Azure e garantir que eles estejam protegidos pela central de segurança do Azure:

- **O agente de Log Analytics deve ser instalado em suas máquinas de arco do Azure baseadas no Windows (versão prévia)**
- **O agente de Log Analytics deve ser instalado em seus computadores de Arc do Azure baseados em Linux (versão prévia)**

Essas novas recomendações aparecerão nos mesmos quatro controles de segurança que a recomendação (relacionada) existente, o **agente de monitoramento deverá ser instalado em seus computadores**: corrigir as configurações de segurança, aplicar o controle de aplicativo adaptável, aplicar atualizações do sistema e habilitar o Endpoint Protection.

As recomendações também incluem a capacidade de correção rápida para ajudar a acelerar o processo de implantação. 

Saiba mais sobre essas duas novas recomendações na tabela de [recomendações de computação e aplicativo](recommendations-reference.md#recs-computeapp) .

Saiba mais sobre como a central de segurança do Azure usa o agente em [o que é o agente de log Analytics?](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent).

Saiba mais sobre [extensões para máquinas de Arc do Azure](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).



### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Novas políticas para criar configurações contínuas de exportação e automação de fluxo de trabalho em escala

Automatizar os processos de monitoramento e resposta a incidentes de sua organização pode melhorar muito o tempo necessário para investigar e atenuar incidentes de segurança.

Para implantar suas configurações de automação em sua organização, use essas políticas internas do Azure ' DeployIfdNotExist ' para criar e configurar procedimentos de [automação de fluxo de trabalho](workflow-automation.md) e [exportação contínuas](continuous-export.md) :

As políticas podem ser encontradas na política do Azure:


|Goal  |Política  |ID da Política  |
|---------|---------|---------|
|Exportação contínua para o Hub de eventos|[Implantar a exportação para o Hub de Eventos para os alertas e as recomendações Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Exportação contínua para o espaço de trabalho Log Analytics|[Implantar a exportação para o workspace do Log Analytics para os alertas e as recomendações da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automação de fluxo de trabalho para alertas de segurança|[Implantar a Automação de Fluxo de Trabalho para alertas da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automação de fluxo de trabalho para recomendações de segurança|[Implantar a Automação de Fluxo de Trabalho para recomendações da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Introdução aos [modelos de automação de fluxo de trabalho](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Saiba mais sobre como usar as duas políticas de exportação em [Exportar continuamente alertas e recomendações da central de segurança do Azure por meio da política](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nova recomendação para usar o NSGs para proteger máquinas virtuais não voltadas para a Internet

O controle de segurança "implementar práticas recomendadas de segurança" agora inclui a nova recomendação a seguir:

- **Máquinas virtuais não voltadas para a Internet devem ser protegidas com grupos de segurança de rede**

Uma recomendação existente, **máquinas virtuais voltadas para a Internet devem ser protegidas com grupos de segurança de rede**, não distinguir entre VMs voltadas para a Internet e não para a Internet. Para ambos, uma recomendação de alta gravidade foi gerada se uma VM não foi atribuída a um grupo de segurança de rede. Essa nova recomendação separa os computadores não voltados para a Internet para reduzir os falsos positivos e evitar alertas de alta severidade desnecessários.

Saiba mais na tabela de [recomendações de rede](recommendations-reference.md#recs-network) .




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Novas políticas para habilitar a proteção contra ameaças e a segurança de dados avançada

As novas políticas abaixo foram adicionadas à iniciativa padrão ASC e foram projetadas para ajudar a habilitar a proteção contra ameaças ou a segurança avançada de dados para os tipos de recursos relevantes.

As políticas podem ser encontradas na política do Azure:


| Política                                                                                                                                                                                                                                                                | ID da Política                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [A Segurança de Dados Avançada deve ser habilitada nos servidores do Banco de Dados SQL do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [A Segurança de Dados Avançada deve ser habilitada nos servidores SQL em computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [A Proteção Avançada contra Ameaças deve ser habilitada em contas de Armazenamento do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos cofres do Azure Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos planos do Serviço de Aplicativo do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos registros do Registro de Contêiner do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos clusters do Serviço de Kubernetes do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [A proteção avançada contra ameaças deve ser habilitada nas Máquinas Virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Saiba mais sobre a [proteção contra ameaças na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/threat-protection).





## <a name="may-2020"></a>Maio de 2020

As atualizações no podem incluir:
- [Regras de supressão de alertas (versão prévia)](#alert-suppression-rules-preview)
- [A avaliação de vulnerabilidades de máquinas virtuais já está em disponibilidade geral](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Alterações no acesso à VM (máquina virtual) JIT (Just-In-Time)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [As recomendações personalizadas foram movidas para um controle de segurança separado](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Adicionada alternância para exibir recomendações em controles ou como uma lista simples](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Controle de segurança expandido "Implementar melhores práticas de segurança"](#expanded-security-control-implement-security-best-practices)
- [Políticas personalizadas com metadados personalizados agora estão em disponibilidade geral](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Migração das funcionalidades de análise de despejo de memória para a detecção de ataque sem arquivos](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Regras de supressão de alertas (versão prévia)

Esse novo recurso (atualmente em versão prévia) ajuda a reduzir a fadiga causada por alertas. Use regras para ocultar automaticamente os alertas que são conhecidos como inócuos ou relacionados a atividades normais em sua organização. Isso permite que você se concentre nas ameaças mais relevantes. 

Os alertas que corresponderem às regras de supressão habilitadas ainda serão gerados, mas o estado deles será definido como ignorado. Você poderá ver o estado no portal do Azure ou de qualquer outra maneira que você acessar os alertas de segurança da Central de Segurança.

As regras de supressão definem os critérios para os quais os alertas devem ser automaticamente ignorados. Normalmente, você usaria uma regra de supressão para:

- suprimir alertas que você identificou como falsos positivos

- suprimir alertas que estão sendo disparados com frequência demais para serem úteis

Saiba mais sobre a [supressão de alertas da Proteção contra Ameaças da Central de Segurança do Azure](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>A avaliação de vulnerabilidades de máquinas virtuais já está em disponibilidade geral

A camada Standard da Central de Segurança agora inclui uma avaliação de vulnerabilidade integrada para máquinas virtuais sem cobrança de nenhum valor adicional. Essa extensão é da plataforma Qualys, mas relata as descobertas dela diretamente para a Central de Segurança. Você não precisa de uma licença do Qualys nem de uma conta do Qualys: tudo é tratado diretamente na Central de Segurança.

A nova solução pode verificar continuamente suas máquinas virtuais para encontrar vulnerabilidades e apresentar as descobertas na Central de Segurança. 

Para implantar a solução, use a nova recomendação de segurança:

"Habilitar a solução de avaliação de vulnerabilidades interna nas máquinas virtuais (da plataforma Qualys)"

Saiba mais sobre [avaliação de vulnerabilidade integrada da Central de Segurança para máquinas virtuais](built-in-vulnerability-assessment.md).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Alterações no acesso à VM (máquina virtual) JIT (Just-In-Time)

A Central de Segurança inclui um recurso opcional para proteger as portas de gerenciamento de suas VMs. Isso fornece uma defesa contra a forma mais comum de ataques de força bruta.

Essa atualização traz as seguintes alterações para esse recurso:

- A recomendação que aconselha você a habilitar o JIT em uma VM foi renomeada. O que anteriormente era "O controle de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais" agora é: "As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede Just-In-Time".

- A recomendação será disparada apenas se houver portas de gerenciamento abertas.

Saiba mais sobre o [recurso de acesso JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>As recomendações personalizadas foram movidas para um controle de segurança separado

Um controle de segurança introduzido com a pontuação segura aprimorada foi "implementar práticas recomendadas de segurança". Todas as recomendações personalizadas criadas para suas assinaturas foram colocadas automaticamente nesse controle. 

Para facilitar a localização de suas recomendações personalizadas, nós as movemos para um controle de segurança dedicado, "Recomendações personalizadas". Este controle não tem impacto sobre sua classificação de segurança.

Saiba mais sobre os controles de segurança em [Classificação de segurança aprimorada (versão prévia) na Central de Segurança do Azure](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Adicionada alternância para exibir recomendações em controles ou como uma lista simples

Os controles de segurança são grupos lógicos de recomendações de segurança relacionadas. Eles refletem suas superfícies de ataque vulneráveis. Um controle é um conjunto de recomendações de segurança, com instruções que ajudam a implementar essas recomendações.

Para ver imediatamente como sua organização está protegendo cada superfície de ataque individual, examine as pontuações de cada controle de segurança.

Por padrão, suas recomendações são mostradas nos controles de segurança. A partir dessa atualização, você também pode exibi-las como uma lista. Para vê-las como uma lista simples classificada pelo status de integridade dos recursos afetados, use a nova alternância 'Agrupar por controles'. A alternância está acima da lista no portal.

Os controles de segurança (e essa alternância) são parte da nova experiência de classificação de segurança. Lembre-se de enviar seus comentários de dentro do portal.

Saiba mais sobre os controles de segurança em [Classificação de segurança aprimorada (versão prévia) na Central de Segurança do Azure](secure-score-security-controls.md).

![Alternância de "Agrupar por controles" para recomendações](\media\secure-score-security-controls\recommendations-group-by-toggle.gif)

### <a name="expanded-security-control-implement-security-best-practices"></a>Controle de segurança expandido "Implementar melhores práticas de segurança" 

Um controle de segurança introduzido com a pontuação segura aprimorada é "implementar práticas recomendadas de segurança". Quando uma recomendação está nesse controle, ela não afeta a classificação de segurança. 

Com essa atualização, três recomendações foram movidas dos controles nos quais foram originalmente colocadas e transferidas para esse controle de práticas recomendadas. Realizamos esta etapa porque determinamos que o risco dessas três recomendações é menor do que se imaginava inicialmente.

Além disso, duas novas recomendações foram introduzidas e adicionadas a esse controle.

As três recomendações que foram movidas são:

- **A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura** (originalmente, no controle "Habilitar MFA")
- **Contas externas com permissões de leitura devem ser removidas de sua assinatura** (originalmente no controle "Gerenciar acesso e permissões")
- **Um máximo de três proprietários deve ser designado para sua assinatura** (originalmente no controle "Gerenciar acesso e permissões")

As duas novas recomendações adicionadas ao controle são:

- A **extensão de configuração de convidado deve ser instalada em máquinas virtuais do Windows (versão prévia)** -usando [Azure Policy configuração de convidado](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) fornece visibilidade dentro de máquinas virtuais para configurações de servidor e aplicativo (somente Windows).

- O **Windows Defender Exploit Guard deve estar habilitado em seus computadores (visualização)** -o Windows Defender Exploit Guard aproveita o agente de configuração de convidado Azure Policy. O Exploit Guard tem quatro componentes que são projetados para bloquear dispositivos contra uma ampla variedade de vetores de ataque e comportamentos de bloqueio comumente usados em ataques de malware, permitindo que cada empresa encontre um equilíbrio entre os respectivos requisitos de produtividade e o risco de segurança que enfrenta (somente Windows).

Saiba mais sobre o Microsoft Defender Exploit Guard em [Criar e implantar uma política do Exploit Guard](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Saiba mais sobre os controles de segurança na [Pontuação segura aprimorada (visualização)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Políticas personalizadas com metadados personalizados agora estão em disponibilidade geral

As políticas personalizadas agora fazem parte da experiência de recomendações da Central de Segurança, da classificação de segurança e do painel de padrões de conformidade regulatória. Esse recurso agora está em disponibilidade geral e permite que você estenda a cobertura de avaliação de segurança da sua organização na Central de Segurança. 

Crie uma iniciativa personalizada no Azure Policy, adicione políticas a ela, integre-a à Central de Segurança do Azure e visualize-a como recomendações.

Agora também adicionamos a opção para editar os metadados de recomendação personalizados. As opções de metadados incluem severidade, etapas de correção, informações sobre ameaças e muito mais.  

Saiba mais sobre [como aprimorar as recomendações personalizadas com informações detalhadas](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Migração das funcionalidades de análise de despejo de memória para a detecção de ataque sem arquivos 

Estamos integrando as funcionalidades de detecção do CDA (análise de despejo de memória) do Windows à [detecção de ataque sem arquivos](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless). A análise de detecção de ataque sem arquivos acompanha versões aprimoradas dos seguintes alertas de segurança para computadores Windows: Injeção de código descoberta, Módulo do Windows simulado detectado, Shellcode descoberto e Segmento de código suspeito detectado.

Alguns dos benefícios dessa transição:

- **Detecção de malware proativa e oportuna** -a abordagem CDA envolvida aguardando a ocorrência de uma falha e, em seguida, executando a análise para localizar artefatos mal-intencionados. O uso da detecção de ataque sem arquivos introduz a identificação proativa de ameaças na memória enquanto elas estão em execução. 

- **Alertas aprimorados** – os alertas de segurança da detecção de ataque sem arquivos incluem aprimoramentos que não estão disponíveis no CDA, como as informações de conexões de rede ativas. 

- **Agregação de alerta** – quando o CDA detecta vários padrões de ataque em um despejo de memória, ele disparou vários alertas de segurança. A detecção de ataque sem arquivos combina todos os padrões de ataque identificados do mesmo processo em um alerta, eliminando a necessidade de correlacionar vários alertas.

- **Requisitos reduzidos em seu workspace do Log Analytics** – despejos de memória que contêm dados potencialmente confidenciais não serão mais carregados em seu workspace do Log Analytics.



## <a name="april-2020"></a>Abril de 2020

As atualizações em abril incluem:
- [Os pacotes de conformidade dinâmica agora estão em disponibilidade geral](#dynamic-compliance-packages-are-now-generally-available)
- [Recomendações de identidade agora incluídas na camada gratuita da Central de Segurança do Azure](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Os pacotes de conformidade dinâmica agora estão em disponibilidade geral

O painel de conformidade regulatória da Central de Segurança do Azure agora inclui **pacotes de conformidade dinâmica** (agora em disponibilidade geral) para acompanhar padrões adicionais do setor e regulatórios.

Os pacotes de conformidade dinâmica podem ser adicionados à sua assinatura ou grupo de gerenciamento na página política de segurança da Central de Segurança. Quando você tiver integrado um padrão ou um parâmetro de comparação, o padrão será exibido em seu painel de conformidade regulatória com todos os dados de conformidade associados mapeados como avaliações. Um relatório de resumo para qualquer um dos padrões que foram integrados estará disponível para download.

Agora, você pode adicionar padrões como:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official e UK NHS**
- **PBMM Federal do Canadá**
- **Azure CIS 1.1.0 (novo)** (que é uma representação mais completa do Azure CIS 1.1.0)

Além disso, adicionamos recentemente o **Azure Security Benchmark**, as diretrizes específicas do Azure criadas pela Microsoft para melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. Outros padrões se tornarão compatíveis com o painel à medida que forem disponibilizados.  
 
Saiba mais sobre [como personalizar o conjunto de padrões em seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Recomendações de identidade agora incluídas na camada gratuita da Central de Segurança do Azure

As recomendações de segurança para identidade e acesso na camada gratuita da Central de Segurança do Azure agora estão em disponibilidade geral. Isso faz parte do esforço para tornar gratuitos os recursos do CSPM (Gerenciamento da situação de segurança na nuvem). Até agora, essas recomendações só estavam disponíveis no tipo de preço Standard.

Exemplos de recomendações de identidade e acesso incluem:

- "A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura."
- "Um máximo de três proprietários deve ser designado para sua assinatura."
- "As contas preteridas devem ser removidas de sua assinatura."

Se você tiver assinaturas no tipo de preço gratuito, suas classificações de segurança serão afetadas por essa alteração, pois essas assinaturas nunca foram avaliadas quanto à identidade e segurança de acesso.

Saiba mais sobre [recomendações de identidade e acesso](recommendations-reference.md#recs-identity).

Saiba mais sobre o [monitoramento de identidade e acesso](security-center-identity-access.md).


## <a name="march-2020"></a>Março de 2020

As atualizações em março incluem:
- [A automação do fluxo de trabalho já está em disponibilidade geral](#workflow-automation-is-now-generally-available)
- [Integração da Central de Segurança do Azure ao Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Proteção para o Serviço de Kubernetes do Azure](#protection-for-azure-kubernetes-service)
- [Experiência Just-In-Time aprimorada](#improved-just-in-time-experience)
- [Duas recomendações de segurança para aplicativos Web preteridas](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>A automação do fluxo de trabalho já está em disponibilidade geral

O recurso de automação de fluxo de trabalho da Central de Segurança do Azure agora está em disponibilidade geral. Use-o para disparar automaticamente Aplicativos Lógicos em alertas de segurança e recomendações. Além disso, os gatilhos manuais estão disponíveis para alertas e todas as recomendações que têm a opção de correção rápida disponível.

Cada programa de segurança inclui vários fluxos de trabalho para resposta a incidentes. Esses processos podem incluir a notificação de stakeholders relevantes, a inicialização de um processo de gerenciamento de alterações e a aplicação de etapas de correção específicas. Os especialistas em segurança recomendam que você automatize o máximo possível de etapas desses procedimentos. A automação reduz a sobrecarga e pode aprimorar sua segurança, garantindo que as etapas do processo sejam feitas de maneira rápida, consistente e de acordo com seus requisitos predefinidos.

Para obter mais informações sobre as funcionalidades automáticas e manuais da Central de Segurança do Azure para executar seus fluxos de trabalho, confira [automação de fluxo de trabalho](workflow-automation.md).

Saiba mais sobre a [criação de Aplicativos Lógicos](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integração da Central de Segurança do Azure ao Windows Admin Center

Agora é possível mover seus servidores Windows locais do Windows Admin Center diretamente para a Central de Segurança do Azure. A Central de Segurança se torna seu único painel de controle para ver informações de segurança para todos os seus recursos do Windows Admin Center, incluindo servidores locais, máquinas virtuais e cargas de trabalho de PaaS adicionais.

Depois de mover um servidor do Windows Admin Center para a Central de Segurança do Azure, você poderá:

- Exibir alertas de segurança e recomendações na extensão da Central de Segurança do Windows Admin Center.
- Exibir a postura de segurança e recuperar informações detalhadas adicionais dos servidores gerenciados do Windows Admin Center na Central de Segurança dentro do portal do Azure (ou por meio de uma API).

Saiba mais sobre [como integrar a Central de Segurança do Azure ao Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Proteção para o Serviço de Kubernetes do Azure

A Central de Segurança do Azure está expandindo os respectivos recursos de segurança de contêiner para proteger o AKS (Serviço de Kubernetes do Azure).

A popular plataforma de software livre do Kubernetes foi adotada tão amplamente que ela agora é um padrão do setor para orquestração de contêineres. Apesar dessa implementação generalizada, ainda há uma falta de compreensão sobre como proteger um ambiente do Kubernetes. A defesa das superfícies de ataque de um aplicativo em contêineres exige experiência para garantir que a infraestrutura seja configurada de modo seguro e constante para possíveis ameaças.

A defesa da Central de Segurança inclui:

- **Descoberta e visibilidade** – descoberta contínua de instâncias gerenciadas do AKS nas assinaturas registradas na Central de Segurança.
- **Recomendações de segurança** – recomendações acionáveis para ajudá-lo a manter a conformidade com as melhores práticas de segurança para o AKS. Essas recomendações estão incluídas na sua classificação de segurança para garantir que elas sejam exibidas como parte da postura de segurança de sua organização. Um exemplo de uma recomendação relacionada ao AKS que você pode ver é "O controle de acesso baseado em função deve ser usado para restringir o acesso a um cluster do serviço de Kubernetes".
- **Proteção contra ameaças** – por meio da análise contínua de sua implantação do AKS, a Central de Segurança alerta você sobre ameaças e atividades mal-intencionadas detectadas no nível de cluster do host e do AKS.

Saiba mais sobre a [Integração do Serviço de Kubernetes do Azure à Central de Segurança](azure-kubernetes-service-integration.md).

Saiba mais sobre os [recursos de segurança de contêiner na Central de Segurança](container-security.md).


### <a name="improved-just-in-time-experience"></a>Experiência Just-In-Time aprimorada

Os recursos, a operação e a interface do usuário das ferramentas Just-In-Time da Central de Segurança do Azure que protegem suas portas de gerenciamento foram aprimoradas da seguinte maneira: 

- **Campo de justificativa** – ao solicitar acesso a uma VM (máquina virtual) por meio da página Just-In-Time do portal do Azure, um novo campo opcional estará disponível para inserir uma justificativa para a solicitação. As informações inseridas nesse campo podem ser acompanhadas no log de atividades. 
- **Limpeza automática de regras JIT (Just-In-Time) redundantes** – sempre que você atualiza uma política JIT, uma ferramenta de limpeza é executada automaticamente para verificar a validade de todo o conjunto de regras. A ferramenta procura incompatibilidades entre as regras em sua política e as regras no NSG. Se a ferramenta de limpeza encontrar uma incompatibilidade, ela determinará a causa e, quando for seguro, removerá as regras internas que não forem mais necessárias. O limpador nunca exclui regras que você criou. 

Saiba mais sobre o [recurso de acesso JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Duas recomendações de segurança para aplicativos Web preteridas

Duas recomendações de segurança relacionadas a aplicativos Web estão sendo preteridas: 

- As regras para aplicativos Web nos NSGs da IaaS devem ser fortalecidas.
    (Política relacionada: as regras de NSGs para aplicativos Web em IaaS devem ser fortalecidas)

- O acesso aos Serviços de Aplicativos deve ser restrito.
    (Política relacionada: o acesso aos Serviços de Aplicativos deve ser restrito [Versão Prévia])

Essas recomendações não serão mais exibidas na lista de recomendações da Central de Segurança. As políticas relacionadas não serão mais incluídas na iniciativa denominada "Padrão da Central de Segurança".

Saiba mais sobre [recomendações de segurança](recommendations-reference.md).

