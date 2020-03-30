---
title: Gerenciar recursos com o Microsoft Graph
titleSuffix: Azure AD B2C
description: Prepare-se para gerenciar os recursos do Azure AD B2C com o Microsoft Graph registrando um aplicativo que concedeu as permissões de API do Graph necessárias.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32117d4bfcf0c0af94eced095b94ab0c1b6f88af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184326"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Gerenciar a azure AD B2C com gráfico microsoft

[O Microsoft Graph][ms-graph] permite que você gerencie muitos dos recursos dentro do seu inquilino Azure AD B2C, incluindo contas de usuários do cliente e políticas personalizadas. Ao escrever scripts ou aplicativos que chamam a API do [Microsoft Graph,][ms-graph-api]você pode automatizar tarefas de gerenciamento de inquilinos como:

* Migre uma loja de usuário existente para um inquilino Azure AD B2C
* Implante políticas personalizadas com um Pipeline Azure no Azure DevOps e gerencie chaves de diretiva personalizadas
* Hospede o registro do usuário em sua própria página e crie contas de usuário em seu diretório Azure AD B2C nos bastidores
* Automatize o registro de aplicativos
* Obter registros de auditoria

As seções a seguir ajudam você a se preparar para usar a API do Microsoft Graph para automatizar o gerenciamento de recursos em seu diretório Azure AD B2C.

## <a name="microsoft-graph-api-interaction-modes"></a>Modos de interação da API do Microsoft Graph

Existem dois modos de comunicação que você pode usar ao trabalhar com a API do Microsoft Graph para gerenciar recursos no seu inquilino Azure AD B2C:

* **Interativo** - Apropriado para tarefas em execução, você usa uma conta de administrador no inquilino B2C para executar as tarefas de gerenciamento. Este modo requer que um administrador faça login usando suas credenciais antes de chamar a API do Microsoft Graph.

* **Automatizado** - Para tarefas programadas ou executadas continuamente, este método usa uma conta de serviço que você configura com as permissões necessárias para executar tarefas de gerenciamento. Você cria a "conta de serviço" no Azure AD B2C registrando um aplicativo que seus aplicativos e scripts usam para autenticar usando seu *ID de aplicativo (Cliente)* e a concessão de credenciais de cliente OAuth 2.0. Neste caso, o aplicativo atua como ele próprio para chamar a API do Microsoft Graph, não o usuário administrador como no método interativo descrito anteriormente.

Você habilita o cenário de interação **automatizada** criando um registro de aplicativo mostrado nas seções a seguir.

## <a name="register-management-application"></a>Registre o aplicativo de gerenciamento

Antes que seus scripts e aplicativos possam interagir com a [API do Microsoft Graph][ms-graph-api] para gerenciar os recursos do Azure AD B2C, você precisa criar um registro de aplicativo no seu inquilino Azure AD B2C que concede as permissões de API necessárias.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>Conceder acesso à API

Em seguida, conceda as permissões de aplicativo registradas para manipular recursos de inquilinos através de chamadas para a API do Microsoft Graph.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Criar segredo do cliente

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Agora você tem um aplicativo que tem permissão para *criar,* *ler,* *atualizar*e *excluir* usuários no seu inquilino Azure AD B2C. Continue até a próxima seção para adicionar permissões de atualização de *senha.*

## <a name="enable-user-delete-and-password-update"></a>Habilite a exclusão do usuário e a atualização de senha

A permissão *de dados do diretório Ler e gravar* **NÃO** inclui a capacidade de excluir usuários ou atualizar senhas da conta de usuário.

Se seu aplicativo ou script precisar excluir usuários ou atualizar suas senhas, atribua a função *de administrador* do Usuário ao seu aplicativo:

1. Faça login no [portal Azure](https://portal.azure.com) e use o filtro **Diretório + Assinatura** para mudar para o seu inquilino Azure AD B2C.
1. Procure e selecione **Azure AD B2C**.
1. Em **Gerenciar,** selecione **Funções e administradores**.
1. Selecione a função de administrador do **usuário.**
1. Selecione **Adicionar atribuições**.
1. Na caixa de texto **Selecionar,** digite o nome do aplicativo que você registrou anteriormente, por exemplo, *managementapp1*. Selecione seu aplicativo quando ele aparecer nos resultados da pesquisa.
1. Selecione **Adicionar**. Pode levar alguns minutos para as permissões se propagarem completamente.

## <a name="next-steps"></a>Próximas etapas

Agora que você registrou seu aplicativo de gerenciamento e concedeu-lhe as permissões necessárias, seus aplicativos e serviços (por exemplo, Azure Pipelines) podem usar suas credenciais e permissões para interagir com a API do Microsoft Graph.

* [Operações B2C suportadas pelo Microsoft Graph](microsoft-graph-operations.md)
* [Gerenciar contas de usuários Azure AD B2C com o Microsoft Graph](manage-user-accounts-graph-api.md)
* [Obtenha registros de auditoria com a API de relatórios de relatórios do Azure](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
