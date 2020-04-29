---
title: Autorizar o acesso à configuração do Azure App usando Azure Active Directory
description: Habilitar o RBAC para autorizar o acesso à sua instância de configuração do Azure App
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77472616"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autorizar o acesso à configuração do Azure App usando Azure Active Directory
Azure App configuração dá suporte ao uso de Azure Active Directory (AD do Azure) para autorizar solicitações para instâncias de configuração de aplicativo.  O Azure AD permite que você use o RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança.  Uma entidade de segurança pode ser um usuário ou uma [entidade de serviço de aplicativo](../active-directory/develop/app-objects-and-service-principals.md).  Para saber mais sobre funções e atribuições de função, confira [noções básicas sobre funções diferentes](../role-based-access-control/overview.md).

## <a name="overview"></a>Visão geral
As solicitações feitas pela entidade de segurança (um usuário ou um aplicativo) para acessar um recurso de configuração de aplicativo devem ser autorizadas.  Com o Azure AD, o acesso a um recurso é um processo de duas etapas.
1. A identidade da entidade de segurança é autenticada e um token OAuth 2,0 é retornado.  O nome do recurso para solicitar um token `https://login.microsoftonline.com/{tenantID}` é `{tenantID}` onde corresponde à ID de locatário Azure Active Directory à qual a entidade de serviço pertence.
2. O token é passado como parte de uma solicitação para o serviço de configuração de aplicativo para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que uma solicitação de aplicativo contenha um token de acesso OAuth 2,0 em tempo de execução.  Se um aplicativo estiver em execução em uma entidade do Azure, como um aplicativo Azure Functions, um aplicativo Web do Azure ou uma VM do Azure, ele poderá usar uma identidade gerenciada para acessar os recursos.  Para saber como autenticar solicitações feitas por uma identidade gerenciada para Azure App configuração, consulte [autenticar o acesso a recursos de configuração do Azure app com Azure Active Directory e identidades gerenciadas para recursos do Azure](howto-integrate-azure-managed-service-identity.md).

A etapa de autorização requer que uma ou mais funções RBAC sejam atribuídas à entidade de segurança. Azure App configuração fornece funções RBAC que abrangem conjuntos de permissões para recursos de configuração de aplicativo. As funções atribuídas a uma entidade de segurança determinam as permissões fornecidas para a entidade. Para obter mais informações sobre as funções RBAC, consulte [funções RBAC internas para Azure app configuração](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Atribuir funções RBAC para direitos de acesso
Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md).

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso é definido para o recurso de configuração de aplicativo. Uma entidade de segurança do Azure AD pode ser um usuário ou uma entidade de serviço de aplicativo ou uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Funções RBAC internas para configuração de Azure App
O Azure fornece as seguintes funções RBAC internas para autorizar o acesso aos dados de configuração do aplicativo usando o Azure AD e o OAuth:

- Proprietário do Azure App de dados de configuração: Use essa função para conceder acesso de leitura/gravação aos recursos de configuração do aplicativo.
- Azure App o leitor de dados de configuração: Use essa função para conceder acesso de leitura aos recursos de configuração do aplicativo.
- Colaborador: Use essa função para dar acesso de administrador ao serviço sem conceder acesso aos dados armazenados na instância de configuração do aplicativo.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como usar [identidades gerenciadas](howto-integrate-azure-managed-service-identity.md) para administrar seu serviço de configuração de aplicativo.