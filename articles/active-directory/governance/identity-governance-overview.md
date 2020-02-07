---
title: Governança de identidade-Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance permite que você Equilibre a necessidade da sua organização de segurança e produtividade dos funcionários com os processos e a visibilidade certos.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd319dd6a83a392f6df26d07a58be22a9c8bdb61
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063672"
---
# <a name="what-is-azure-ad-identity-governance"></a>O que é o Azure AD Identity Governance?

A governança de identidade Azure Active Directory (Azure AD) permite equilibrar a necessidade da sua organização de segurança e produtividade dos funcionários com os processos e a visibilidade certos. Ele fornece recursos para garantir que as pessoas certas tenham o acesso certo aos recursos certos. Esses recursos relacionados ao Azure AD e Enterprise Mobility + Security permitem que você reduza o risco de acesso protegendo, monitorando e auditando o acesso a ativos críticos, garantindo, ao mesmo tempo, a produtividade dos funcionários e dos parceiros comerciais.  

A governança de identidade fornece às organizações a capacidade de realizar as seguintes tarefas entre funcionários, parceiros de negócios e fornecedores e entre serviços e aplicativos locais e em nuvens:

- Controlar o ciclo de vida de identidade
- Controlar o ciclo de vida de acesso
- Proteger o acesso privilegiado para administração

Especificamente, destina-se a ajudar as organizações a abordar estas quatro perguntas importantes:

- Quais usuários devem ter acesso a quais recursos?
- O que esses usuários estão fazendo com esse acesso?
- Existem controles organizacionais em vigor para gerenciar o acesso?
- Auditores podem verificar se os controles estão funcionando?

## <a name="identity-lifecycle"></a>Ciclo de vida de identidade

A governança de identidade ajuda as organizações a alcançar um equilíbrio entre a *produtividade* – com que rapidez uma pessoa pode ter acesso aos recursos de que precisam, como quando elas se unem à minha organização? E *segurança* – Como o acesso deve mudar ao longo do tempo, como devido a alterações ao status de emprego da pessoa?  O gerenciamento do ciclo de vida da identidade é a base para governança de identidade, e a governança eficaz em escala requer modernização da infraestrutura de gerenciamento do ciclo de vida de identidade para aplicativos.

![Ciclo de vida de identidade](./media/identity-governance-overview/identity-lifecycle.png)

Para muitas organizações, o ciclo de vida de identidade para funcionários está vinculado à representação daquele usuário em um sistema HCM (gerenciamento de capital humano).  Azure AD Premium mantém automaticamente as identidades de usuário para as pessoas representadas no workday tanto no Active Directory quanto no Azure Active Directory, conforme descrito no [tutorial de provisionamento de entrada do workday](../saas-apps/workday-inbound-tutorial.md).  O Azure AD Premium também inclui o [Microsoft Identity Manager](/microsoft-identity-manager/), que pode importar registros de sistemas HCM locais como SAP, Oracle eBusiness e Oracle PeopleSoft.

Cada vez mais, os cenários exigem a colaboração com pessoas de fora da sua organização. A colaboração [B2B do Azure AD](/azure/active-directory/b2b/) permite compartilhar com segurança aplicativos e serviços da sua organização com usuários convidados e parceiros externos de qualquer organização, ao mesmo tempo mantendo o controle sobre seus próprios dados corporativos.  O [Gerenciamento de direitos do Azure ad](entitlement-management-overview.md) permite que você Selecione quais usuários da organização têm permissão para solicitar acesso e são adicionados como convidados B2B ao diretório da sua organização e garante que esses convidados sejam removidos quando não precisarem mais de acesso.

## <a name="access-lifecycle"></a>Ciclo de vida de acesso

As organizações precisam de um processo para gerenciar o acesso além do que foi inicialmente provisionado para um usuário quando a identidade do usuário foi criada.  Além disso, organizações empresariais precisam ser capazes de dimensionar com eficiência para poderem desenvolver e impor política de acesso e controles continuamente.

![Ciclo de vida de acesso](./media/identity-governance-overview/access-lifecycle.png)

Em geral, a IT delega as decisões de aprovação de acesso aos tomadores de decisão empresariais.  Além disso, a IT pode envolver os próprios usuários.  Por exemplo, os usuários que acessam os dados confidenciais do cliente no aplicativo de marketing da empresa na Europa precisam conhecer as políticas da empresa. Usuários convidados podem não estar cientes dos requisitos de manipulação para os dados em uma organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso por meio de tecnologias como [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md), junto com o provisionamento de usuário para [aplicativos SaaS](../saas-apps/tutorial-list.md) ou [aplicativos integrados ao SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md).  As organizações também podem controlar quais [usuários convidados têm acesso a aplicativos locais](../b2b/hybrid-cloud-to-on-premises.md).  Esses direitos de acesso podem então ser revisados regularmente usando [revisões de acesso do Azure AD](access-reviews-overview.md) recorrentes.   O [Gerenciamento de direitos do Azure ad](entitlement-management-overview.md) também permite que você defina como os usuários solicitam acesso entre pacotes de associações de grupo e de equipe, funções de aplicativo e funções do SharePoint Online.

Quando um usuário tenta acessar aplicativos, o Azure AD impõe políticas de [acesso condicional](/azure/active-directory/conditional-access/) . Por exemplo, as políticas de acesso condicional podem incluir a exibição de [termos de uso](../conditional-access/terms-of-use.md) e [a garantia de que o usuário concordou com esses termos](../conditional-access/require-tou.md) antes de poder acessar um aplicativo.

## <a name="privileged-access-lifecycle"></a>Ciclo de vida de acesso privilegiado

Historicamente, o acesso privilegiado foi descrito por outros fornecedores como um recurso separado da governança de identidade. No entanto, na Microsoft, acreditamos que o controle de acesso privilegiado é uma parte fundamental do controle de identidades, especialmente considerando o potencial para o uso inadequado de usuários associados a esses direitos de administrador pode causar uma organização. Funcionários, fornecedores e prestadores de serviço que assumem direitos administrativos precisam ser controlados.

![Ciclo de vida de acesso privilegiado](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) fornece controles adicionais adaptados para proteger os direitos de acesso para recursos, no Azure AD, no Azure e em outros serviços online da Microsoft.  O acesso just-in-time e os recursos de alerta de alteração de função fornecidos pelo Azure AD PIM, além da autenticação multifator e do acesso condicional, fornecem um conjunto abrangente de controles de governança para ajudar a proteger os recursos da sua empresa (diretório, Office 365 e funções de recurso do Azure). Assim como acontece com outras formas de acesso, as organizações podem usar revisões de acesso para configurar a recertificação recorrente de acesso para todos os usuários nas funções de administrador.

## <a name="getting-started"></a>Introdução

Confira a guia introdução do **controle de identidade** no portal do Azure para começar a usar o gerenciamento de direitos, revisões de acesso, Privileged Identity Management e termos de uso.

![Introdução à governança de identidade](./media/identity-governance-overview/getting-started.png)


Se você tiver algum comentário sobre os recursos de governança de identidade, clique em **comentários obtidos?** no portal do Azure para enviar seus comentários. A equipe revisa seus comentários regularmente.

Embora não haja uma solução perfeita ou recomendação para todos os clientes, os seguintes guias de configuração também fornecem as políticas de linha de base que a Microsoft recomenda que você siga para garantir uma força de ti mais segura e produtiva.

- [Configurações de acesso à identidade e ao dispositivo](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Protegendo o acesso privilegiado](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Apêndice-funções com privilégios mínimos para gerenciar em recursos de governança de identidade

É uma prática recomendada usar a função menos privilegiada para executar tarefas administrativas no controle de identidade. Recomendamos que você use o Azure AD PIM para ativar uma função conforme necessário para executar essas tarefas. A seguir estão as funções de diretório menos privilegiadas para configurar os recursos de governança de identidade:

| Recurso | Função com privilégios mínimos |
| ------- | --------------------- |
| Gerenciamento de direitos | Administrador do usuário (com exceção da adição de sites do SharePoint Online a catálogos, que requer administrador global) |
| Análises de acesso | Administrador do usuário (com exceção das revisões de acesso das funções do Azure ou do Azure AD, que exige administrador de função com privilégios) |
|Privileged Identity Management | Administrador de função com privilégios |
| Termos de uso | Administradores de segurança ou administrador de acesso condicional |

## <a name="next-steps"></a>Próximas etapas

- [O que é o gerenciamento de direitos do Azure AD?](entitlement-management-overview.md)
- [Quais são as revisões de acesso do Azure AD?](access-reviews-overview.md)
- [O que é o Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [O que posso fazer com os Termos de uso?](active-directory-tou.md)


