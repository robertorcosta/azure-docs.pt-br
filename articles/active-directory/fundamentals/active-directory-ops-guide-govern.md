---
title: Guia de referência de operações de governança do Azure Active Directory
description: Este guia de referência de operações descreve as verificações e ações que você deve tomar para garantir o gerenciamento de governança
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535451"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Guia de referência de operações de governança do Azure Active Directory

Esta seção do guia de referência de [operações azure AD](active-directory-ops-guide-intro.md) descreve as verificações e ações que você deve tomar para avaliar e atestar o acesso concedido a identidades não privilegiadas e privilegiadas, alterações de auditoria e controle no ambiente.

> [!NOTE]
> Essas recomendações são atuais a partir da data de publicação, mas podem mudar com o tempo. As organizações devem avaliar continuamente suas práticas de governança à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas-chave

O gerenciamento do Azure Active Directory requer a execução contínua das principais tarefas e processos operacionais, que podem não fazer parte de um projeto de implantação. Ainda é importante que você configure essas tarefas para otimizar seu ambiente. As principais tarefas e seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Arquive os registros de auditoria do Azure AD no sistema SIEM | Equipe de Operações InfoSec |
| Descubra aplicativos que são gerenciados fora da conformidade | Equipe de Operações IAM |
| Revisar regularmente o acesso aos aplicativos | Equipe de Arquitetura InfoSec |
| Revisar regularmente o acesso a identidades externas | Equipe de Arquitetura InfoSec |
| Revisar regularmente quem tem papéis privilegiados | Equipe de Arquitetura InfoSec |
| Defina portões de segurança para ativar funções privilegiadas | Equipe de Arquitetura InfoSec |
| Revisar regularmente as concessões de consentimento | Equipe de Arquitetura InfoSec |
| Design Catálogos e Pacotes de Acesso para aplicativos e recursos baseados em funcionários da organização | Proprietários de aplicativos |
| Defina políticas de segurança para atribuir usuários a pacotes de acesso | Equipe InfoSec + Proprietários de Aplicativos |
| Se as políticas incluem fluxos de trabalho de aprovação, revise regularmente as aprovações do fluxo de trabalho | Proprietários de aplicativos |
| Revisar exceções em políticas de segurança, como políticas de acesso condicional, usando revisões de acesso | Equipe de Operações InfoSec |

Ao revisar sua lista, você pode achar que precisa atribuir um proprietário para tarefas que estão faltando a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estão alinhados com as recomendações acima.

#### <a name="owner-recommended-reading"></a>Leitura recomendada do proprietário

- [Atribuindo funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governança no Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>Teste de alterações de configuração

Existem mudanças que requerem considerações especiais ao testar, desde técnicas simples como a implantação de um subconjunto de usuários de destino até a implantação de uma alteração em um inquilino de teste paralelo. Se você não implementou uma estratégia de teste, você deve definir uma abordagem de teste com base nas diretrizes da tabela abaixo:

| Cenário| Recomendação |
|-|-|
|Alterando o tipo de autenticação de federado para PHS/PTA ou vice-versa| Use [o rollout encenado](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) para testar o impacto da alteração do tipo de autenticação.|
|Implantação de uma nova política de acesso condicional (CA) ou política de proteção de identidade|Crie uma nova diretiva de CA e designe para testar usuários.|
|Onboarding em um ambiente de teste de um aplicativo|Adicione o aplicativo a um ambiente de produção, esconda-o do painel MyApps e atribua-o aos usuários durante a fase de garantia de qualidade (QA).|
|Mudança de regras de sincronização|Execute as alterações em um teste Azure AD Connect com a mesma configuração que está atualmente em produção, também conhecido como modo de preparação, e analise os resultados do CSExport. Se satisfeito, troque para a produção quando estiver pronto.|
|Mudança de marca|Teste em um inquilino de teste separado.|
|Lançando um novo recurso|Se o recurso for suportado para um conjunto de usuários-alvo, identifique os usuários piloto e construa. Por exemplo, a redefinição de senha de autoatendimento e a autenticação de vários fatores podem atingir usuários ou grupos específicos.|
|Corte um aplicativo de um provedor de identidade local (IdP), por exemplo, Active Directory, para o Azure AD|Se o aplicativo suportar várias configurações de IdP, por exemplo, Salesforce, configure ambos e teste o Azure AD durante uma janela de alteração (caso o aplicativo introduza a página HRD). Se o aplicativo não suportar vários IDPs, agende os testes durante uma janela de controle de alteração e o tempo de inatividade do programa.|
|Atualizar regras de grupo dinâmico|Crie um grupo dinâmico paralelo com a nova regra. Compare com o resultado calculado, por exemplo, execute o PowerShell com a mesma condição.<br>Se o teste passar, troque os locais onde o grupo antigo foi usado (se viável).|
|Migrar licenças de produtos|Consulte [Alterar a licença para um único usuário em um grupo licenciado no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses).|
|Alterar as regras do AD FS, como Autorização, Emissão, MFA|Use a reivindicação de grupo para segmentar subconjunto de usuários.|
|Alterar a experiência de autenticação do AD FS ou alterações semelhantes em toda a fazenda|Crie uma fazenda paralela com o mesmo nome de host, implemente alterações de configuração, teste de clientes usando arquivo HOSTS, regras de roteamento NLB ou roteamento semelhante.<br>Se a plataforma de destino não suportar arquivos HOSTS (por exemplo, dispositivos móveis), altere o controle.|

## <a name="access-reviews"></a>Análises de acesso

### <a name="access-reviews-to-applications"></a>Avaliações de acesso a aplicativos

Com o tempo, os usuários podem acumular acesso aos recursos à medida que se deslocam por diferentes equipes e posições. É importante que os proprietários de recursos revisem o acesso aos aplicativos regularmente e removam privilégios que não são mais necessários durante todo o ciclo de vida dos usuários. As [avaliações](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) de acesso ao Azure AD permitem que as organizações gerenciem eficientemente membros de grupos, acesso a aplicativos corporativos e atribuições de função. Os proprietários de recursos devem revisar o acesso dos usuários regularmente para garantir que apenas as pessoas certas tenham acesso contínuo. Idealmente, você deve considerar o uso de avaliações de acesso a Azure AD para esta tarefa.

![Comentários de acesso página inicial](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Cada usuário que interage com as avaliações de acesso deve ter uma licença Azure AD Premium P2 paga.

### <a name="access-reviews-to-external-identities"></a>Revisões de acesso a identidades externas

É fundamental manter o acesso a identidades externas restrita apenas aos recursos necessários, durante o tempo necessário. Estabeleça um processo regular de revisão de acesso automatizado para todas as identidades externas e acesso a aplicativos usando [avaliações](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)de acesso a Ad do Azure. Se um processo já existir no local, considere usar as avaliações de acesso do Azure AD. Uma vez que um aplicativo seja aposentado ou não seja mais usado, remova todas as identidades externas que tiveram acesso ao aplicativo.

> [!NOTE]
> Cada usuário que interage com as avaliações de acesso deve ter uma licença Azure AD Premium P2 paga.

## <a name="privileged-account-management"></a>Gerenciamento de contas com privilégios

### <a name="privileged-account-usage"></a>Uso privilegiado da conta

Os hackers geralmente têm como alvo contas de admin e outros elementos de acesso privilegiado para obter rapidamente acesso a dados e sistemas confidenciais.Uma vez que os usuários com funções privilegiadas tendem a se acumular ao longo do tempo, é importante rever e gerenciar o acesso ao administrador regularmente e fornecer acesso privilegiado just-in-time aos recursos do Azure AD e do Azure.

Se não houver nenhum processo em sua organização para gerenciar contas privilegiadas ou você tiver atualmente admins que usam suas contas de usuário regulares para gerenciar serviços e recursos, você deve começar imediatamente a usar contas separadas, por exemplo, uma para o dia-a-dia regular atividades; o outro para acesso privilegiado e configurado com MFA. Melhor ainda, se sua organização tem uma assinatura Azure AD Premium P2, então você deve implantar imediatamente [o Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM). No mesmo token, você também deve rever essas contas privilegiadas e [atribuir funções menos privilegiadas,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) se aplicável.

Outro aspecto da gestão de contas privilegiadas que deve ser implementado é na definição de [revisões de acesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) para essas contas, manualmente ou [automatizadas através do PIM.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review)

#### <a name="privileged-account-management-recommended-reading"></a>Gerenciamento de contas privilegiada recomendada leitura

- [Funções no Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>Contas de acesso de emergência

As organizações devem criar [contas de emergência](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) para estarem preparadas para gerenciar o Azure AD para casos como paralisações de autenticação como:

- Componentes de paralisação de infra-estruturas de autenticação (AD FS, AD on-premises, serviço MFA)
- Rotatividade de pessoal administrativo

Para evitar ser bloqueado inadvertidamente do seu inquilino porque você não pode entrar ou ativar a conta de um usuário individual existente como administrador, você deve criar duas ou mais contas de emergência e garantir que elas sejam implementadas e alinhadas com [as práticas recomendadas da Microsoft](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) e [procedimentos de quebra de vidro.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)

### <a name="privileged-access-to-azure-ea-portal"></a>Acesso privilegiado ao portal Azure EA

O [portal Azure Enterprise Agreement (Azure EA)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) permite criar assinaturas do Azure contra um contrato corporativo mestre, que é um papel poderoso dentro da empresa. É comum a criação deste portal antes mesmo de colocar o Azure AD no lugar, por isso é necessário usar as identidades do Azure AD para bloqueá-lo, remover contas pessoais do portal, garantir que a delegação adequada esteja no lugar e mitigar o risco de bloqueio .

Para ficar claro, se o nível de autorização do portal Da EA estiver atualmente definido como "modo misto", você deve remover quaisquer [contas da Microsoft](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) de qualquer acesso privilegiado no portal da EA e configurar o portal Da EA apenas para usar contas Azure AD. Se as funções delegadas do portal EA não forem configuradas, você também deve encontrar e implementar funções delegadas para departamentos e contas.

#### <a name="privileged-access-recommended-reading"></a>Leitura recomendada de acesso privilegiado

- [Permissões da função de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>Gerenciamento de direitos

[O gerenciamento de direitos (EM)](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) permite que os proprietários de aplicativos empacotem recursos e os atribuam a personas específicas na organização (tanto internas quanto externas). O EM permite o autoatendimento se inscrever e delegação aos proprietários de empresas, mantendo políticas de governança para conceder acesso, definir durações de acesso e permitir fluxos de trabalho de aprovação. 

> [!NOTE]
> O Azure AD Entitlement Management requer licenças Azure AD Premium P2.

## <a name="summary"></a>Resumo

Há oito aspectos para uma governança de identidade segura. Esta lista ajudará você a identificar as ações que você deve tomar para avaliar e atestar o acesso concedido a identidades não privilegiadas e privilegiadas, auditar e controlar alterações no ambiente.

- Designe proprietários para tarefas-chave.
- Implementar uma estratégia de teste.
- Use o Azure AD Access Reviews para gerenciar eficientemente as associações de grupos, o acesso a aplicativos corporativos e atribuições de função.
- Estabeleça um processo regular e automatizado de revisão de acesso para todos os tipos de identidades externas e acesso a aplicativos.
- Estabeleça um processo de revisão de acesso para revisar e gerenciar o acesso ao administrador regularmente e fornecer acesso privilegiado just-in-time aos recursos do Azure AD e do Azure.
- Prover contas de emergência para serem preparadas para gerenciar o Azure AD para paralisações inesperadas.
- Bloqueie o acesso ao portal Azure EA.
- Implementar a Gestão de Direitos para fornecer acesso regido a uma coleção de recursos.

## <a name="next-steps"></a>Próximas etapas

Comece com as [verificações e ações operacionais do Azure AD.](active-directory-ops-guide-ops.md)
