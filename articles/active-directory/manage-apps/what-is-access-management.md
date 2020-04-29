---
title: Gerenciando o acesso a aplicativos que usam o Azure AD | Microsoft Docs
description: Descreve como o Active Directory do Azure permite que as organizações especifiquem os aplicativos aos quais cada usuário tem acesso.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 489b15423add03d69070bc32057af97396a85309
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409059"
---
# <a name="managing-access-to-apps"></a>Gerenciando o acesso a aplicativos

Gerenciamento de acesso contínuo, avaliação de uso e relatórios continuam a ser um desafio depois que um aplicativo é integrado ao sistema de identidade da sua organização. Em muitos casos, os administradores de TI ou a assistência técnica deve ter uma função ativa contínua no gerenciamento de acesso aos seus aplicativos. Às vezes, a atribuição é executada por uma equipe de TI geral ou de divisão. Frequentemente, a decisão de atribuição deve ser delegada ao tomador de decisões comerciais, exigindo sua aprovação antes de a TI fazer a atribuição.  Outras organizações investem na integração com um sistema de gerenciamento automatizado de identidade e acesso existente, como RBAC (controle de acesso baseado em função) ou ABAC (controle de acesso baseado em atributos). A integração e o desenvolvimento de regras tendem a ser especializados e caros. Em cada abordagem de gerenciamento, monitoramento e relatórios exigem um investimento separado, caro e complexo.

## <a name="how-does-azure-active-directory-help"></a>Como o Active Directory do Azure ajuda?

O AD do Azure oferece suporte a gerenciamento de acesso abrangente para aplicativos configurados, permitindo que as organizações obtenham facilmente as políticas de acesso corretas desde a atribuição automática, com base em atributo (cenários de ABAC ou RBAC) por meio da delegação e incluindo gerenciamento de administradores. Com o AD do Azure, você pode facilmente obter políticas complexas, combinando vários modelos de gerenciamento de um único aplicativo e pode até reutilizar regras de gerenciamento entre aplicativos com o mesmo público.

Com o AD do Azure, relatórios de atribuição e uso são totalmente integrados, permitindo que os administradores facilmente criem relatórios sobre estado da atribuição, erros de atribuição e até mesmo uso.

### <a name="assigning-users-and-groups-to-an-app"></a>Atribuindo usuários e grupos a um aplicativo

A atribuição de aplicativo do AD do Azure se concentra em dois modos de atribuição principais:

* **Atribuição individual** Um administrador de TI com permissões de Administrador Global de diretório pode selecionar as contas de usuário individuais e conceder a elas acesso ao aplicativo.

* **Atribuição baseada em grupo (requer Azure ad Premium P1 ou P2)** Um administrador de ti com permissões de administrador global de diretório pode atribuir um grupo ao aplicativo. O acesso de usuários específicos é determinado dependendo se eles são membros do grupo no momento em que tenta acessar o aplicativo. Em outras palavras, um administrador pode efetivamente criar uma regra de atribuição informando que "qualquer membro atual do grupo atribuído tem acesso ao aplicativo". Usando essa opção de atribuição, os administradores podem se beneficiar de qualquer uma das opções de gerenciamento de grupo do Azure AD, incluindo [grupos dinâmicos baseados em atributo](../fundamentals/active-directory-groups-create-azure-portal.md), grupos de sistema externo (por exemplo, Active Directory local ou Workday) ou grupos gerenciados de autoatendimento ou gerenciados pelo administrador. Um único grupo pode ser atribuído facilmente a vários aplicativos, garantindo que os aplicativos com afinidade de atribuição possam compartilhar as regras de atribuição, reduzindo a complexidade do gerenciamento geral. Note que associações de grupo aninhadas não têm suporte para atribuição com base em grupo de aplicativos atualmente.

Usando esses dois modos de atribuição, os administradores podem obter qualquer abordagem de gerenciamento de atribuição desejada.

### <a name="requiring-user-assignment-for-an-app"></a>Exigindo a atribuição de usuário para um aplicativo

Com determinados tipos de aplicativos, você tem a opção de [exigir que os usuários sejam atribuídos ao aplicativo](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). Ao fazer isso, você impede que todos façam logon, exceto os usuários atribuídos explicitamente ao aplicativo. Os seguintes tipos de aplicativos oferecem suporte a essa opção:

* Aplicativos configurados para SSO (logon único) federado com autenticação baseada em SAML
* Aplicativos de proxy de aplicativo que usam Azure Active Directory pré-autenticação
* Aplicativos criados na plataforma de aplicativo do Azure AD que usam autenticação OAuth 2,0/OpenID Connect depois que um usuário ou administrador consentiu nesse aplicativo. Determinados aplicativos empresariais oferecem controle adicional sobre quem tem permissão para entrar.

Quando a atribuição de usuário *não é necessária*, os usuários não atribuídos não veem o aplicativo no painel de acesso meus aplicativos, mas eles ainda podem entrar no próprio aplicativo (também conhecido como logon iniciado pelo SP) ou podem usar a URL de **acesso do usuário** na página de **Propriedades** do aplicativo (também conhecido como logon iniciado pelo IDP).

Para alguns aplicativos, a opção de exigir a atribuição de usuário não está disponível nas propriedades do aplicativo. Nesses casos, você pode usar o PowerShell para definir a propriedade appRoleAssignmentRequired na entidade de serviço.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Determinando a experiência do usuário para acessar aplicativos

O Azure AD fornece [várias maneiras personalizáveis de implantar aplicativos](end-user-experiences.md) para usuários finais em sua organização:

* Painel de acesso de meus aplicativos do Azure AD
* Iniciador de aplicativos do Office 365
* Logon direto para aplicativos federados (serviço-PR)
* Links profundos a aplicativos federados, baseado em senha, ou existentes

Você pode determinar se os usuários atribuídos a um aplicativo empresarial podem vê-lo no painel de acesso e no iniciador de aplicativos do Office 365.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Exemplo: atribuição de aplicativo complexo com o Azure AD
Considere um aplicativo como o Salesforce. Em muitas organizações, o Salesforce é usado principalmente pelas equipes de marketing e vendas. Geralmente, membros da equipe de marketing têm acesso altamente privilegiado ao Salesforce, enquanto os membros da equipe de vendas têm acesso limitado. Em muitos casos, uma grande população de operadores de informações tem acesso restrito ao aplicativo. Exceções a essas regras complicam as coisas. Geralmente é privilégio das equipes de liderança de marketing ou vendas conceder acesso a um usuário ou alterar suas funções independentemente dessas regras genéricas.

Com o AD do Azure, aplicativos como o Salesforce podem ser pré-configurados para logon único (SSO) e provisionamento automatizado. Quando o aplicativo é configurado, um administrador pode realizar a ação única de criar e atribuir os grupos apropriados. Neste exemplo, um administrador pode executar as atribuições a seguir:

* [grupos dinâmicos](../fundamentals/active-directory-groups-create-azure-portal.md) podem ser definidos para representar automaticamente todos os membros das equipes de marketing e vendas usando atributos como o departamento ou a função:
  
  * Todos os membros de grupos de marketing seriam atribuídos à função de "marketing" no Salesforce
  * Todos os membros dos grupos da equipe de vendas seriam atribuídos à função de "vendas" no Salesforce. Um ajuste adicional poderia usar vários grupos que representam equipes de vendas regionais que são atribuídos a funções diferentes no Salesforce.

* Para habilitar o mecanismo de exceção, um grupo de autoatendimento poderia ser criado para cada função. Por exemplo, o grupo "exceção de marketing do Salesforce" pode ser criado como um grupo de autoatendimento. O grupo pode ser atribuído à função de marketing do Salesforce e a equipe de liderança de marketing pode ser definida como proprietária. Membros da equipe de liderança de marketing podem adicionar ou remover usuários, definir uma política de associação ou até mesmo aprovar ou negar as solicitações de usuários individuais para ingressar. Este mecanismo é suportado por meio de uma experiência adequada ao operador de informações que não exige treinamento especializado para os proprietários ou membros.

Nesse caso, todos os usuários atribuídos poderiam ser provisionados automaticamente no Salesforce. Como são adicionados a grupos diferentes, sua atribuição de função seria atualizada no Salesforce. Os usuários poderiam descobrir e acessar o Salesforce por meio do painel de acesso de aplicativos Microsoft, clientes do Office na web ou até mesmo navegando para sua página de logon organizacional do Salesforce. Os administradores poderiam exibir facilmente o status de atribuição e uso usando os relatórios do AD do Azure.

Os administradores podem empregar o [acesso condicional do Azure ad](../active-directory-conditional-access-azure-portal.md) para definir políticas de acesso para funções específicas. Essas políticas podem incluir se o acesso é permitido fora do ambiente corporativo e até mesmo requisitos de autenticação multifator ou dispositivo para obter acesso em vários casos.

## <a name="access-to-microsoft-applications"></a>Acesso a aplicativos da Microsoft

Os aplicativos Microsoft (como o Office 365 Exchange, SharePoint, Yammer, etc.) são atribuídos e gerenciados de forma um pouco diferente dos aplicativos SaaS de terceiros ou outros aplicativos que você integra com o Azure AD para o logon único.

Há três principais maneiras que um usuário pode obter acesso a um aplicativo publicado Microsoft.

- Para aplicativos no Office 365 ou em outros conjuntos de aplicativos pagos, os usuários têm acesso através de **atribuição de licença** diretamente à sua conta de usuário ou através de um grupo que usa nosso recurso de atribuição de licenças baseada em grupo.
- Para aplicativos que a Microsoft ou uma terceira parte publica livremente para qualquer pessoa usar, os usuários podem receber acesso por meio do [consentimento do usuário](configure-user-consent.md). Isso significa que eles entram no aplicativo com sua conta corporativa ou de estudante do Azure AD e permitem que ele tenha acesso a algum conjunto limitado de dados em sua conta.
- Para aplicativos que a Microsoft ou uma terceira parte publica livremente para qualquer pessoa usar, os usuários também podem receber acesso por meio do [consentimento do administrador](manage-consent-requests.md). Isso significa que um administrador determinou que o aplicativo pode ser usado por todos na organização, portanto, entram no aplicativo com uma conta de Administrador Global e concedem acesso a todos na organização.

Alguns aplicativos combinam esses métodos. Por exemplo, determinados aplicativos da Microsoft fazem parte de uma assinatura do Office 365, mas ainda exigem consentimento.

Os usuários podem acessar aplicativos do Office 365 por meio de seus portais do Office 365. Você também pode mostrar ou ocultar aplicativos do Office 365 no painel de acesso meus aplicativos com a [alternância de visibilidade do office 365](hide-application-from-user-portal.md) nas **configurações de usuário**do seu diretório. 

Assim como acontece com os aplicativos empresariais, você pode [atribuir usuários](assign-user-or-group-access-portal.md) a determinados aplicativos da Microsoft por meio da portal do Azure ou, se a opção do portal não estiver disponível, usando o PowerShell.

## <a name="next-steps"></a>Próximas etapas
* [Protegendo aplicativos com acesso condicional](../active-directory-conditional-access-azure-portal.md)
* [Gerenciamento de grupo de autoatendimento/SSAA](../users-groups-roles/groups-self-service-management.md)
