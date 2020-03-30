---
title: Use o portal Azure para acessar dados de blob ou fila
titleSuffix: Azure Storage
description: Quando você acessa dados de blob ou fila usando o portal Azure, o portal faz solicitações ao Azure Storage as capas. Essas solicitações ao Azure Storage podem ser autenticadas e autorizadas usando sua conta Azure AD ou a chave de acesso da conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 602be49ef0c60274f1cd016c4f8e870cf033ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866907"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Use o portal Azure para acessar dados de blob ou fila

Quando você acessa dados de blob ou fila usando o [portal Azure,](https://portal.azure.com)o portal faz solicitações ao Azure Storage as capas. Uma solicitação ao Azure Storage pode ser autorizada usando sua conta Azure AD ou a chave de acesso da conta de armazenamento. O portal indica qual método você está usando e permite que você alterne entre os dois se você tiver as permissões apropriadas.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Permissões necessárias para acessar dados de blob ou fila

Dependendo de como você deseja autorizar o acesso a dados de blob ou fila no portal Azure, você precisará de permissões específicas. Na maioria dos casos, essas permissões são fornecidas via RBAC (Role-Based Access Control). Para obter mais informações sobre o RBAC, consulte [O que é o controle de acesso baseado em função (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Chave de acesso à conta

Para acessar dados blob e fila com a chave de acesso à conta, você deve ter uma função RBAC atribuída a você que inclua a ação RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Esta função RBAC pode ser uma função incorporada ou personalizada. Funções incorporadas que suportam **microsoft.storage/storageContas/listkeys/action** incluem:

- A função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) do azure resource manager
- A função de [contribuinte](../../role-based-access-control/built-in-roles.md#contributor) do gerente de recursos do Azure
- A função [contribuinte da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Quando você tenta acessar dados de blob ou fila no portal Dozure, o portal primeiro verifica se você foi atribuído uma função com **microsoft.Armazenamento/armazenamentoContas/listas/ações**. Se você foi designado para uma função com essa ação, então o portal usa a chave da conta para acessar dados de blob e fila. Se você não tiver sido designado para um papel com essa ação, então o portal tentará acessar dados usando sua conta Azure AD.

> [!NOTE]
> As funções clássicas de administrador de assinatura Administrador e Co-Administrador incluem o equivalente à função de proprietário do [gerenciador](../../role-based-access-control/built-in-roles.md#owner) de recursos do Azure. A função **Proprietário** inclui todas as ações, incluindo o **Microsoft.Storage/storageAccounts/listkeys/action,** para que um usuário com uma dessas funções administrativas também possa acessar dados de blob e fila com a chave da conta. Para obter mais informações, consulte [funções clássicas de administrador de assinaturas](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Conta do AD do Azure

Para acessar os dados do blob ou da fila do portal Azure usando sua conta Azure AD, ambas as seguintes declarações devem ser verdadeiras para você:

- Você foi designado para a função Azure Resource Manager [Reader,](../../role-based-access-control/built-in-roles.md#reader) no mínimo, escopo para o nível da conta de armazenamento ou superior. A função **Reader** concede as permissões mais restritas, mas outra função de Gerenciador de Recursos do Azure que concede acesso aos recursos de gerenciamento de contas de armazenamento também é aceitável.
- Você foi designado para uma função incorporada ou personalizada que fornece acesso a dados de blob ou fila.

A atribuição de função **Reader** ou outra atribuição de função do Azure Resource Manager é necessária para que o usuário possa visualizar e navegar os recursos de gerenciamento de contas de armazenamento no portal Dozure. As funções RBAC que concedem acesso a dados de blob ou fila não concedem acesso aos recursos de gerenciamento de contas de armazenamento. Para acessar dados de blob ou fila no portal, o usuário precisa de permissões para navegar nos recursos da conta de armazenamento. Para obter mais informações sobre esse requisito, consulte [Atribuir a função Leitor para acesso ao portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

As funções incorporadas que suportam o acesso aos seus dados de blob ou fila incluem:

- [Armazenamento Blob Data Owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Para controle de acesso POSIX para Azure Data Lake Storage Gen2.
- [Armazenamento Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Read/write/delete permissões para blobs.
- [Leitor de dados Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Permissões somente de leitura para bolhas.
- [Contribuinte de dados da fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Permissões de leitura/gravação/exclusão para filas.
- [Leitor de dados da fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): permissões somente de leitura para filas.
    
As funções personalizadas podem suportar diferentes combinações das mesmas permissões fornecidas pelas funções incorporadas. Para obter mais informações sobre a criação de funções RBAC personalizadas, consulte [Funções personalizadas para os recursos do Azure](../../role-based-access-control/custom-roles.md) e [entenda as definições de função para os recursos do Azure](../../role-based-access-control/role-definitions.md).

As filas de listagem com uma função clássica de administrador de assinatura não são suportadas. Para listar filas, um usuário deve ter atribuído a eles a função Azure Resource Manager **Reader,** a função **Leitor de dados da fila de armazenamento** ou a função Contribuinte de dados da fila de **armazenamento.**

> [!IMPORTANT]
> A versão de visualização do Storage Explorer no portal Azure não suporta o uso de credenciais Azure AD para visualizar e modificar dados de blob ou fila. O Storage Explorer no portal Azure sempre usa as chaves da conta para acessar dados. Para usar o Storage Explorer no portal Azure, você deve ser atribuído a uma função que inclua **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navegue até blobs ou filas no portal

Para visualizar dados de blob ou fila no portal, navegue até a **visão geral da** sua conta de armazenamento e clique nos links para **Blobs** ou **Filas**. Alternativamente, você pode navegar para as seções **de serviço Blob** e **Fila** no menu. 

![Navegue para blob ou dados de fila no portal Azure](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Determine o método de autenticação atual

Quando você navega para um contêiner ou uma fila, o portal Azure indica se você está usando a chave de acesso da conta ou sua conta Azure AD para autenticar.

Os exemplos nesta seção mostram acessar um contêiner e suas bolhas, mas o portal exibe a mesma mensagem quando você está acessando uma fila e suas mensagens, ou listando filas.

### <a name="account-access-key"></a>Chave de acesso à conta

Se você estiver autenticando usando a chave de acesso da conta, você verá **a Chave** de Acesso especificada como o método de autenticação no portal:

![Atualmente acessando dados de contêiner com a chave da conta](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Para mudar para usar a conta Azure AD, clique no link destacado na imagem. Se você tiver as permissões apropriadas através das funções RBAC que são atribuídas a você, você poderá prosseguir. No entanto, se você não tiver as permissões certas, você verá uma mensagem de erro como a seguinte:

![Erro mostrado se a conta Azure AD não suportar acesso](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Observe que nenhuma bolha aparecerá na lista se sua conta Azure AD não tiver permissões para visualizá-las. Clique no Switch para acessar o **link-chave** para usar a chave de acesso para autenticação novamente.

### <a name="azure-ad-account"></a>Conta do AD do Azure

Se você estiver autenticando usando sua conta Azure AD, você verá **a Conta de Usuário Azure AD** especificada como o método de autenticação no portal:

![Atualmente acessando dados de contêiner com a conta Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Para mudar para usar a tecla de acesso da conta, clique no link destacado na imagem. Se você tiver acesso à chave da conta, então você poderá prosseguir. No entanto, se você não tiver acesso à chave da conta, você verá uma mensagem de erro como a seguinte:

![Erro mostrado se você não tiver acesso à chave da conta](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Observe que não aparecerão bolhas na lista se você não tiver acesso às chaves da conta. Clique no link **'Switch to Azure AD User Account'** para usar sua conta Azure AD para autenticação novamente.

## <a name="next-steps"></a>Próximas etapas

- [Autenticar o acesso a bolhas e filas do Azure usando o Azure Active Directory](storage-auth-aad.md)
- [Conceda acesso a contêineres e filas do Azure com RBAC no portal Azure](storage-auth-aad-rbac-portal.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC usando a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados de blob e fila do Azure com RBAC usando PowerShell](storage-auth-aad-rbac-powershell.md)
