---
title: Autorize o acesso à configuração do aplicativo Azure usando o Azure Active Directory
description: Habilite o RBAC para autorizar o acesso à sua instância de configuração do aplicativo Azure
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472616"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autorize o acesso à configuração do aplicativo Azure usando o Azure Active Directory
A configuração do aplicativo azure suporta o uso do Azure Active Directory (Azure AD) para autorizar solicitações às instâncias de configuração do aplicativo.  O Azure AD permite que você use o RBAC (Role-Based Access Control, controle de acesso baseado em função) para conceder permissões a um diretor de segurança.  Um diretor de segurança pode ser um usuário ou um [diretor de serviço de aplicativo.](../active-directory/develop/app-objects-and-service-principals.md)  Para saber mais sobre papéis e atribuições de papéis, consulte [Entendendo diferentes papéis.](../role-based-access-control/overview.md)

## <a name="overview"></a>Visão geral
Solicitações feitas pelo diretor de segurança (um usuário ou um aplicativo) para acessar um recurso de Configuração de Aplicativo devem ser autorizadas.  Com o Azure AD, o acesso a um recurso é um processo de duas etapas.
1. A identidade do diretor de segurança é autenticada e um token OAuth 2.0 é devolvido.  O nome do recurso para `https://login.microsoftonline.com/{tenantID}` `{tenantID}` solicitar um token é onde corresponde ao ID de inquilino do Azure Active Directory ao qual o principal do serviço pertence.
2. O token é passado como parte de uma solicitação ao serviço de configuração do aplicativo para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que uma solicitação de aplicativo contenha um token de acesso OAuth 2.0 em tempo de execução.  Se um aplicativo estiver sendo executado dentro de uma entidade Azure, como um aplicativo Azure Functions, um Azure Web App ou uma VM Azure, ele poderá usar uma identidade gerenciada para acessar os recursos.  Para saber como autenticar solicitações feitas por uma identidade gerenciada na configuração do aplicativo Azure, consulte [Authenticate acesso aos recursos de configuração do aplicativo Azure com o Azure Active Directory e identidades gerenciadas para recursos do Azure](howto-integrate-azure-managed-service-identity.md).

A etapa de autorização exige que uma ou mais funções RBAC sejam atribuídas ao diretor de segurança. A configuração do aplicativo Azure fornece funções RBAC que englobam conjuntos de permissões para recursos de configuração de aplicativos. As funções atribuídas a um diretor de segurança determinam as permissões fornecidas ao diretor. Para obter mais informações sobre as funções RBAC, consulte [funções RBAC incorporadas para configuração do aplicativo Azure](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Atribuir funções RBAC para direitos de acesso
Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md).

Quando uma função RBAC é atribuída a um diretor de segurança do Azure AD, o Azure concede acesso a esses recursos para esse principal de segurança. O acesso é escopo do recurso Configuração do aplicativo. Um diretor de segurança Azure AD pode ser um usuário, ou um diretor de serviço de aplicativo ou uma [identidade gerenciada para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Funções RBAC incorporadas para configuração do aplicativo Azure
O Azure fornece as seguintes funções de RBAC incorporadas para autorizar o acesso aos dados de configuração do aplicativo usando o Azure AD e o OAuth:

- Proprietário de dados de configuração do aplicativo Azure: Use essa função para dar acesso à leitura/gravação aos recursos de configuração do aplicativo.
- Leitor de dados de configuração do aplicativo Azure: Use essa função para dar acesso à leitura dos recursos de configuração do aplicativo.
- Contribuinte: Use essa função para dar acesso ao serviço sem conceder acesso aos dados armazenados na instância de configuração do aplicativo.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como usar [identidades gerenciadas](howto-integrate-azure-managed-service-identity.md) para administrar seu serviço de configuração de aplicativos.