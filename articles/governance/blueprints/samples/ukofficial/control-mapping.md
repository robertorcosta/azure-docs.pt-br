---
title: Exemplo-plantas oficiais do Reino Unido e do Reino Unido do NHS – mapeamento de controle
description: Mapeamento de controle dos exemplos do plano gráfico do Reino Unido e do Reino Unido NHS. Cada controle é mapeado para uma ou mais políticas do Azure que auxiliam na avaliação.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 9001e752c9b0f79d7c688e0b13fd7133004ff4ad
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248796"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Mapeamento de controle dos exemplos do plano gráfico do Reino Unido e do Reino Unido NHS

O artigo a seguir fornece detalhes sobre como os exemplos do plano gráfico do Reino Unido e do Reino Unido NHS são mapeados para os controles OFFICIAL e NHS do Reino Unido. Para obter mais informações sobre os controles, consulte [oficial do Reino Unido](https://www.gov.uk/government/publications/government-security-classifications).

Os mapeamentos a seguir são para os controles **oficial do Reino Unido** e **NHS do Reino Unido** . Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione os **controles \[Preview @ no__t-2 Audit UK e UK NHS e implante extensões de VM específicas para dar suporte** à iniciativa de política interna de requisitos de auditoria.

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../../../policy/overview.md). Essas políticas podem ajudar você a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência 1:1 ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias políticas. Não garante que você está totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre controles e definições do Azure Policy desta amostra de blueprint de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 proteção de dados em trânsito

O plano gráfico ajuda a garantir que a transferência de informações com os serviços do Azure seja segura atribuindo definições de [Azure Policy](../../../policy/overview.md) que auditam conexões inseguras a contas de armazenamento e cache Redis.

- Somente conexões seguras com o Cache Redis devem ser habilitadas
- A transferência segura para as contas de armazenamento deve ser habilitada

## <a name="23-data-at-rest-protection"></a>2,3 proteção de dados em repouso

Este projeto ajuda a reforçar a política sobre o uso de controles cryptograph atribuindo definições de [Azure Policy](../../../policy/overview.md) que impõem controles cryptograph específicos e auditam o uso de configurações de criptografia fracas.
Entender em que local os recursos do Azure podem ter configurações de criptografia não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos sejam configurados de acordo com a política de segurança de informações. Especificamente, as políticas atribuídas por este projeto exigem criptografia para contas de armazenamento do data Lake; exigir Transparent Data Encryption em bancos de dados SQL; auditar criptografia ausente em contas de armazenamento, bancos de dados SQL, discos de máquina virtual e variáveis de conta de automação; auditar conexões inseguras para contas de armazenamento e cache Redis; auditar a criptografia de senha de máquina virtual fraca; e auditar a comunicação de Service Fabric não criptografada.

- A Transparent Data Encryption deve ser habilitada nos bancos de dados SQL
- A criptografia de disco deve ser aplicada nas máquinas virtuais
- As variáveis da conta de automação devem ser criptografadas
- A transferência segura para as contas de armazenamento deve ser habilitada
- A propriedade ClusterProtectionLevel dos clusters do Service Fabric deve ser definida como EncryptAndSign
- A Transparent Data Encryption deve ser habilitada nos bancos de dados SQL
- Implantar a transparent data encryption do BD SQL
- Exigir a criptografia em contas do Data Lake Storage
- Locais permitidos (foram embutidos em código para "sul do Reino Unido" e "oeste do Reino Unido")
- Locais permitidos para grupos de recursos (foi embutido no código "sul do Reino Unido" e "oeste do Reino Unido")

## <a name="52-vulnerability-management"></a>5,2 gerenciamento de vulnerabilidades

Este projeto ajuda você a gerenciar as vulnerabilidades do sistema de informações atribuindo definições de [Azure Policy](../../../policy/overview.md) que monitoram a proteção de ponto de extremidade ausente, atualizações do sistema ausentes, vulnerabilidades do sistema operacional, vulnerabilidades do SQL e virtual vulnerabilidades do computador. Esses insights fornecem informações em tempo real sobre o estado de segurança dos recursos implantados e podem ajudá-lo a priorizar as ações de correção.

- Monitorar Endpoint Protection ausente na Central de Segurança do Azure
- As atualizações do sistema devem ser instaladas nos computadores
- As vulnerabilidades na configuração de segurança dos computadores devem ser corrigidas
- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades

## <a name="53-protective-monitoring"></a>Monitoramento de proteção 5,3

Este projeto ajuda a proteger os ativos do sistema de informações atribuindo definições de [Azure Policy](../../../policy/overview.md) que fornecem monitoramento de proteção de acesso irrestrito, atividade de lista de permissões e ameaças.

- Auditar o acesso irrestrito à rede para contas de armazenamento
- Os Controles de Aplicativo Adaptáveis devem ser habilitados nas máquinas virtuais
- Implantar a Detecção de Ameaças nos servidores SQL
- Implantar a extensão antimalware do Microsoft IaaS padrão para o Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 gerenciamento seguro de usuários/10 identidade e autenticação

O Azure implementa o RBAC (controle de acesso baseado em função) para ajudá-lo a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode examinar quem tem acesso aos recursos do Azure e suas permissões. Este projeto ajuda a restringir e controlar os direitos de acesso atribuindo definições de [Azure Policy](../../../policy/overview.md) para auditar contas externas com permissões de proprietário e/ou permissões de leitura/gravação e contas com permissão de proprietário, leitura e/ou gravação que não têm vários fatores autenticação habilitada.

- A MFA deve ser habilitada nas contas com permissões de proprietário na assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- A MFA deve ser habilitada nas contas com permissões de leitura na assinatura
- As contas externas com permissões de proprietário devem ser removidas da sua assinatura
- As contas externas com permissões de gravação devem ser removidas da sua assinatura
- As contas externas com permissões de leitura devem ser removidas da sua assinatura

Este projeto atribui definições de Azure Policy para auditar o uso de autenticação de Azure Active Directory para SQL Servers e Service Fabric. O uso da autenticação do Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft.

- Um administrador do Azure Active Directory deve ser provisionado para os SQL Servers
- Os clusters do Service Fabric devem usar somente o Azure Active Directory para autenticação de cliente

Esse projeto também atribui definições de Azure Policy para auditar contas que devem ser priorizadas para revisão, incluindo contas depreciadas e contas externas. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), o que remove imediatamente os direitos de acesso aos recursos do Azure. Este projeto atribui duas definições de Azure Policy para auditar a conta depreciada que deve ser considerada para remoção.

- As contas preteridas devem ser removidas da sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas da sua assinatura
- As contas externas com permissões de gravação devem ser removidas da sua assinatura

Esse plano gráfico também atribui uma definição de Azure Policy que audita as permissões de arquivo de senha de VM do Linux para alertar se elas estiverem definidas incorretamente. Esse design permite que você tome medidas corretivas para garantir que os autenticadores não sejam comprometidos.

- \[Versão Prévia\]: Auditar que as permissões do arquivo/etc/passwd de VM Linux estejam definidas para 0644

Este projeto ajuda a impor senhas fortes atribuindo definições de Azure Policy que auditam as VMs do Windows que não impõem a força mínima e outros requisitos de senha. O reconhecimento de VMs que estejam violando a política de força da senha ajuda você a tomar ações corretivas para garantir que as senhas de todas as contas de usuário da VM estejam em conformidade com a política.

- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não têm a configuração de complexidade de senha habilitada
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não têm uma duração máxima da senha de 70 dias
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não têm uma duração mínima da senha de 1 dia
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não restringem o tamanho mínimo da senha a 14 caracteres
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que podem reutilizar as últimas 24 senhas
- \[Versão Prévia\]: Auditar as VMs do Windows que não têm a configuração de complexidade de senha habilitada
- \[Versão Prévia\]: Auditar as VMs do Windows que não têm uma duração máxima da senha de 70 dias
- \[Versão Prévia\]: Auditar as VMs do Windows que não têm uma duração mínima da senha de 1 dia
- \[Versão Prévia\]: Auditar as VMs do Windows que não restringem o tamanho mínimo da senha a 14 caracteres
- \[Versão Prévia\]: Auditar as VMs do Windows que podem reutilizar as 24 senhas anteriores

Este projeto também ajuda a controlar o acesso aos recursos do Azure atribuindo definições de Azure Policy. Essas políticas auditam o uso de tipos de recursos e configurações que podem permitir um acesso mais permissivo aos recursos. Entender os recursos que estão violando essas políticas pode ajudar você a tomar ações corretivas para garantir que o acesso aos recursos do Azure seja restrito a usuários autorizados.

- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Linux que têm contas sem senhas
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Linux que permitem conexões remotas em contas sem senhas
- \[Versão Prévia\]: Auditar as VMs do Linux que têm contas sem senhas
- \[Versão Prévia\]: Auditar as VMs do Linux que permitem conexões remotas em contas sem senhas
- As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager
- As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager
- Auditar VMs que não usam discos gerenciados

## <a name="11-external-interface-protection"></a>11 proteção de interface externa

Além de usar mais de 25 políticas para o gerenciamento de usuários seguro apropriado, este projeto ajuda a proteger interfaces de serviço contra acesso não autorizado, atribuindo uma definição de [Azure Policy](../../../policy/overview.md) que monitora contas de armazenamento irrestritas. As contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações. Esse projeto também atribui uma política que habilita controles de aplicativo adaptáveis em máquinas virtuais.

- Auditar o acesso irrestrito à rede para contas de armazenamento
- Os Controles de Aplicativo Adaptáveis devem ser habilitados nas máquinas virtuais

## <a name="12-secure-service-administration"></a>12 administração segura de serviços

O Azure implementa o RBAC (controle de acesso baseado em função) para ajudá-lo a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode examinar quem tem acesso aos recursos do Azure e suas permissões. Este projeto ajuda a restringir e controlar direitos de acesso privilegiado atribuindo cinco definições de [Azure Policy](../../../policy/overview.md) para auditar contas externas com permissões de proprietário e/ou gravação e contas com o proprietário e/ou permissões de gravação que não têm autenticação multifator habilitada.

Sistemas usados para a administração de um serviço de nuvem têm acesso altamente privilegiado ao serviço. Um comprometimento teria um impacto significativo, incluindo os meios para ignorar os controles de segurança e roubar ou manipular grandes volumes de dados. Os métodos usados pelos administradores do provedor de serviços para gerenciar o serviço operacional devem ser projetados para atenuar qualquer risco de exploração que possa prejudicar a segurança do serviço. Se esse princípio não for implementado, um invasor poderá ter meios para ignorar os controles de segurança e roubar ou manipular grandes volumes de dados.

- A MFA deve ser habilitada nas contas com permissões de proprietário na assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- As contas externas com permissões de proprietário devem ser removidas da sua assinatura
- As contas externas com permissões de gravação devem ser removidas da sua assinatura

Este projeto atribui definições de Azure Policy para auditar o uso de autenticação de Azure Active Directory para SQL Servers e Service Fabric. O uso da autenticação do Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft.

- Um administrador do Azure Active Directory deve ser provisionado para os SQL Servers
- Os clusters do Service Fabric devem usar somente o Azure Active Directory para autenticação de cliente

Esse projeto também atribui definições de Azure Policy para auditar contas que devem ser priorizadas para análise, incluindo contas depreciadas e contas externas com permissões elevadas. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), o que remove imediatamente os direitos de acesso aos recursos do Azure. Este projeto atribui duas definições de Azure Policy para auditar a conta depreciada que deve ser considerada para remoção.

- As contas preteridas devem ser removidas da sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas da sua assinatura
- As contas externas com permissões de gravação devem ser removidas da sua assinatura

Esse plano gráfico também atribui uma definição de Azure Policy que audita as permissões de arquivo de senha de VM do Linux para alertar se elas estiverem definidas incorretamente. Esse design permite que você tome medidas corretivas para garantir que os autenticadores não sejam comprometidos.

- \[Versão Prévia\]: Auditar que as permissões do arquivo/etc/passwd de VM Linux estejam definidas para 0644

## <a name="13-audit-information-for-users"></a>13 informações de auditoria para usuários

Este blueprint ajuda você a garantir que os eventos do sistema sejam registrados em log por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam as configurações do log de auditoria em recursos do Azure. Uma política atribuída também audita se as máquinas virtuais não estão enviando logs para um workspace especificado do Log Analytics.

- A auditoria deve ser habilitada nas configurações de Segurança de Dados Avançada no SQL Server
- Configuração de diagnóstico de auditoria
- Auditar configurações de Auditoria de nível do servidor SQL
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Linux
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Windows
- Implantar o observador de rede quando as redes virtuais são criadas

## <a name="next-steps"></a>Próximas etapas

Agora que você analisou o mapeamento de controle dos planos gráficos do Reino Unido e do Reino Unido NHS, visite os artigos a seguir para saber mais sobre a visão geral e como implantar este exemplo:

> [!div class="nextstepaction"]
> [Plantas NHSis do Reino Unido e do Reino Unido-visão geral](./index.md)
> [ru Official e plantas de NHS do Reino Unido-etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).