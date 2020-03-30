---
title: Governança de Identidade - Diretório Ativo do Azure | Microsoft Docs
description: O Azure Active Directory Identity Governance permite equilibrar a necessidade de segurança e produtividade dos funcionários da sua organização com os processos e a visibilidade certos.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063672"
---
# <a name="what-is-azure-ad-identity-governance"></a>O que é o Azure AD Identity Governance?

A Governança de Identidade do Azure Active Directory (Azure AD) permite equilibrar a necessidade de segurança e produtividade dos funcionários da sua organização com os processos e a visibilidade certos. Ele fornece recursos para garantir que as pessoas certas tenham o acesso certo aos recursos certos. Esses e relacionados recursos de Ad AD e Enterprise Mobility + Security permitem mitigar o risco de acesso protegendo, monitorando e auditando o acesso a ativos críticos -- ao mesmo tempo em que garante a produtividade de funcionários e parceiros de negócios.  

A Governança de Identidade dá às organizações a capacidade de realizar as seguintes tarefas entre funcionários, parceiros de negócios e fornecedores, e em serviços e aplicativos, tanto no local quanto nas nuvens:

- Controlar o ciclo de vida de identidade
- Controlar o ciclo de vida de acesso
- Garantir acesso privilegiado para administração

Especificamente, destina-se a ajudar as organizações a abordar estas quatro perguntas importantes:

- Quais usuários devem ter acesso a quais recursos?
- O que esses usuários estão fazendo com esse acesso?
- Existem controles organizacionais em vigor para gerenciar o acesso?
- Auditores podem verificar se os controles estão funcionando?

## <a name="identity-lifecycle"></a>Ciclo de vida de identidade

Governança de Identidade ajuda as organizações a alcançar um equilíbrio entre *produtividade* - Quão rapidamente uma pessoa pode ter acesso aos recursos de que precisa, como quando entra na minha organização? E *segurança* – Como o acesso deve mudar ao longo do tempo, como devido a alterações ao status de emprego da pessoa?  O gerenciamento do ciclo de vida da identidade é a base para a Governança de Identidade, e a governança eficaz em escala requer a modernização da infra-estrutura de gerenciamento do ciclo de vida da identidade para aplicativos.

![Ciclo de vida de identidade](./media/identity-governance-overview/identity-lifecycle.png)

Para muitas organizações, o ciclo de vida de identidade para funcionários está vinculado à representação daquele usuário em um sistema HCM (gerenciamento de capital humano).  O Azure AD Premium mantém automaticamente as identidades dos usuários para pessoas representadas no Workday tanto no Active Directory quanto no Azure Active Directory, conforme descrito no tutorial de [provisionamento de entrada](../saas-apps/workday-inbound-tutorial.md)do Workday .  O Azure AD Premium também inclui o [Microsoft Identity Manager](/microsoft-identity-manager/), que pode importar registros de sistemas HCM locais como SAP, Oracle eBusiness e Oracle PeopleSoft.

Cada vez mais, os cenários exigem a colaboração com pessoas de fora da sua organização. A colaboração [B2B do Azure AD](/azure/active-directory/b2b/) permite compartilhar com segurança aplicativos e serviços da sua organização com usuários convidados e parceiros externos de qualquer organização, ao mesmo tempo mantendo o controle sobre seus próprios dados corporativos.  [O gerenciamento de direitos do Azure AD](entitlement-management-overview.md) permite que você selecione quais usuários da organização podem solicitar acesso e ser adicionados como convidados B2B ao diretório da sua organização e garante que esses hóspedes sejam removidos quando não precisarem mais de acesso.

## <a name="access-lifecycle"></a>Ciclo de vida de acesso

As organizações precisam de um processo para gerenciar o acesso além do que foi inicialmente provisionado para um usuário quando a identidade do usuário foi criada.  Além disso, organizações empresariais precisam ser capazes de dimensionar com eficiência para poderem desenvolver e impor política de acesso e controles continuamente.

![Ciclo de vida de acesso](./media/identity-governance-overview/access-lifecycle.png)

Em geral, a IT delega as decisões de aprovação de acesso aos tomadores de decisão empresariais.  Além disso, a IT pode envolver os próprios usuários.  Por exemplo, os usuários que acessam os dados confidenciais do cliente no aplicativo de marketing da empresa na Europa precisam conhecer as políticas da empresa. Usuários convidados podem não estar cientes dos requisitos de manipulação para os dados em uma organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso por meio de tecnologias como [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md), junto com o provisionamento de usuário para [aplicativos SaaS](../saas-apps/tutorial-list.md) ou [aplicativos integrados ao SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md).  As organizações também podem controlar quais [usuários convidados têm acesso a aplicativos locais](../b2b/hybrid-cloud-to-on-premises.md).  Esses direitos de acesso podem então ser revisados regularmente usando [revisões de acesso do Azure AD](access-reviews-overview.md) recorrentes.   [O gerenciamento de direitos do Azure AD](entitlement-management-overview.md) também permite definir como os usuários solicitam acesso entre pacotes de membros de grupo e equipe, funções de aplicativos e funções do SharePoint Online.

Quando um usuário tenta acessar aplicativos, o Azure AD aplica políticas [de acesso condicional.](/azure/active-directory/conditional-access/) Por exemplo, as políticas de Acesso Condicional podem incluir a exibição de um [termo de uso](../conditional-access/terms-of-use.md) e a garantia de que o usuário tenha [concordado com esses termos](../conditional-access/require-tou.md) antes de poder acessar um aplicativo.

## <a name="privileged-access-lifecycle"></a>Ciclo de vida de acesso privilegiado

Historicamente, o acesso privilegiado tem sido descrito por outros fornecedores como um recurso separado da Governança de Identidade. No entanto, na Microsoft, achamos que governar o acesso privilegiado é uma parte fundamental da Governança de Identidade -- especialmente dado o potencial de uso indevido associado a esses direitos de administrador que pode causar a uma organização. Funcionários, fornecedores e prestadores de serviço que assumem direitos administrativos precisam ser controlados.

![Ciclo de vida de acesso privilegiado](./media/identity-governance-overview/privileged-access-lifecycle.png)

[O Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) fornece controles adicionais adaptados para garantir direitos de acesso aos recursos, através do Azure AD, Azure e outros Serviços Online da Microsoft.  Os recursos de alerta de alteração de just-in-time e de mudança de função fornecidos pelo Azure AD PIM, além de autenticação multifatorial e acesso condicional, fornecem um conjunto abrangente de controles de governança para ajudar a proteger os recursos da sua empresa (diretório, Funções de recursos do Office 365 e do Azure). Assim como acontece com outras formas de acesso, as organizações podem usar revisões de acesso para configurar a recertificação recorrente de acesso para todos os usuários nas funções de administrador.

## <a name="getting-started"></a>Introdução

Confira a guia Como iniciar a Governança de **Identidade** no portal Azure para começar a usar gerenciamento de direitos, avaliações de acesso, Gerenciamento de Identidade Privilegiada e Termos de Uso.

![Governança de Identidade começando](./media/identity-governance-overview/getting-started.png)


Se você tiver algum feedback sobre os recursos de Governança de Identidade, clique em **Got feedback?** no portal Azure para enviar seu feedback. A equipe revisa regularmente seu feedback.

Embora não haja uma solução ou recomendação perfeita para cada cliente, os seguintes guias de configuração também fornecem as políticas de linha de base que a Microsoft recomenda que você siga para garantir uma força de trabalho mais segura e produtiva.

- [Configurações de acesso à identidade e ao dispositivo](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Protegendo o acesso privilegiado](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Apêndice - funções menos privilegiadas para gerenciar recursos de Governança de Identidade

É uma prática recomendada usar o papel menos privilegiado para executar tarefas administrativas na Governança de Identidade. Recomendamos que você use o Azure AD PIM para ativar uma função conforme necessário para executar essas tarefas. A seguir estão as funções de diretório menos privilegiadas para configurar os recursos de Governança de Identidade:

| Recurso | Função com privilégios mínimos |
| ------- | --------------------- |
| Gerenciamento de direitos | Administrador de usuário (com exceção da adição de sites SharePoint Online a catálogos, o que requer administrador global) |
| Análises de acesso | Administrador de usuário (com exceção das avaliações de acesso das funções Azure ou Azure AD, que requer administrador de funções privilegiadas) |
|Privileged Identity Management | Administrador de função com privilégios |
| Termos de uso | Administrador de segurança ou administrador de acesso condicional |

## <a name="next-steps"></a>Próximas etapas

- [O que é o gerenciamento de direitos do Azure AD?](entitlement-management-overview.md)
- [Quais são as avaliações de acesso ao Azure AD?](access-reviews-overview.md)
- [O que é o Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [O que posso fazer com os Termos de uso?](active-directory-tou.md)


