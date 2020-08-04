---
title: Usar o portal do Azure para acessar dados de BLOB ou de fila
titleSuffix: Azure Storage
description: Quando você acessa dados de BLOB ou de fila usando o portal do Azure, o portal faz solicitações para o armazenamento do Azure nos bastidores. Essas solicitações para o armazenamento do Azure podem ser autenticadas e autorizadas usando sua conta do Azure AD ou a chave de acesso da conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: dcd1280dbe3a00a6a7cbdaaf59aa05326dfa8375
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534168"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Usar o portal do Azure para acessar dados de BLOB ou de fila

Quando você acessa dados de BLOB ou de fila usando o [portal do Azure](https://portal.azure.com), o portal faz solicitações para o armazenamento do Azure nos bastidores. Uma solicitação para o armazenamento do Azure pode ser autorizada usando sua conta do Azure AD ou a chave de acesso da conta de armazenamento. O portal indica qual método você está usando e permite que você alterne entre os dois se tiver as permissões apropriadas.  

Você também pode especificar como autorizar uma operação de upload de blob individual no portal do Azure. Por padrão, o portal usa qualquer método que você já esteja usando para autorizar uma operação de upload de BLOB, mas você tem a opção de alterar essa configuração ao carregar um blob.

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Permissões necessárias para acessar dados de BLOB ou fila

Dependendo de como você deseja autorizar o acesso a dados de BLOB ou de fila no portal do Azure, você precisará de permissões específicas. Na maioria dos casos, essas permissões são fornecidas por meio do RBAC (controle de acesso baseado em função). Para obter mais informações sobre o RBAC, consulte [o que é o Azure RBAC (controle de acesso baseado em função)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Usar a chave de acesso da conta

Para acessar dados de BLOB e de fila com a chave de acesso da conta, você deve ter uma função do Azure atribuída a você que inclua a ação RBAC **Microsoft. Storage/storageAccounts/listkeys/Action**. Essa função do Azure pode ser uma função interna ou personalizada. Funções internas que dão suporte a **Microsoft. Storage/storageAccounts/listkeys/Action** incluem:

- A função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager
- A função [colaborador](../../role-based-access-control/built-in-roles.md#contributor) de Azure Resource Manager
- A função de [colaborador da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Quando você tenta acessar dados de BLOB ou de fila no portal do Azure, o portal verifica primeiro se você recebeu uma função com **Microsoft. Storage/storageAccounts/listkeys/Action**. Se você tiver recebido uma função com essa ação, o portal usará a chave de conta para acessar dados de BLOB e de fila. Se você não tiver recebido uma função com essa ação, o portal tentará acessar os dados usando sua conta do Azure AD.

> [!NOTE]
> O administrador de serviço de funções de administrador de assinatura clássica e o coadministrador incluem o equivalente da função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager. A função de **proprietário** inclui todas as ações, incluindo a **ação Microsoft. Storage/storageAccounts/listkeys/**, para que um usuário com uma dessas funções administrativas também possa acessar dados de BLOB e de fila com a chave de conta. Para obter mais informações, consulte [funções de administrador de assinatura clássica, funções do Azure e funções de administrador do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Usar sua conta do Azure AD

Para acessar dados de BLOB ou de fila do portal do Azure usando sua conta do Azure AD, as duas instruções a seguir devem ser verdadeiras para você:

- Você recebeu a função [leitor](../../role-based-access-control/built-in-roles.md#reader) de Azure Resource Manager, no mínimo, no escopo do nível da conta de armazenamento ou superior. A função **leitor** concede as permissões mais restritas, mas outra função de Azure Resource Manager que concede acesso aos recursos de gerenciamento da conta de armazenamento também é aceitável.
- Você foi atribuído a uma função interna ou personalizada que fornece acesso aos dados de BLOB ou de fila.

A atribuição de função de **leitor** ou outra atribuição de função de Azure Resource Manager é necessária para que o usuário possa exibir e navegar pelos recursos de gerenciamento de conta de armazenamento no portal do Azure. As funções do Azure que concedem acesso a dados de BLOB ou de fila não concedem acesso aos recursos de gerenciamento da conta de armazenamento. Para acessar dados de BLOB ou de fila no portal, o usuário precisa de permissões para navegar pelos recursos da conta de armazenamento. Para obter mais informações sobre esse requisito, consulte [atribuir a função leitor para acesso ao portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

As funções internas que dão suporte ao acesso ao BLOB ou aos dados da fila incluem:

- [Proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): para o controle de acesso POSIX para Azure data Lake Storage Gen2.
- [Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): permissões de leitura/gravação/exclusão para BLOBs.
- [Leitor de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): permissões somente leitura para BLOBs.
- [Colaborador de dados da fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): permissões de leitura/gravação/exclusão para filas.
- [Leitor de dados da fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): permissões somente leitura para filas.

As funções personalizadas podem dar suporte a diferentes combinações das mesmas permissões fornecidas pelas funções internas. Para obter mais informações sobre como criar funções personalizadas do Azure, consulte [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md) e [noções básicas sobre definições de função para recursos do Azure](../../role-based-access-control/role-definitions.md).

Não há suporte para a listagem de filas com uma função de administrador de assinatura clássica. Para listar filas, um usuário deve ter atribuído a eles a função **leitor** de Azure Resource Manager, a função de **leitor de dados fila de armazenamento** ou a função **colaborador de dados da fila de armazenamento** .

> [!IMPORTANT]
> A versão de visualização do Gerenciador de Armazenamento no portal do Azure não oferece suporte ao uso de credenciais do Azure AD para exibir e modificar dados de BLOB ou de fila. Gerenciador de Armazenamento no portal do Azure sempre usa as chaves de conta para acessar dados. Para usar Gerenciador de Armazenamento no portal do Azure, você deve ser atribuído a uma função que inclui **Microsoft. Storage/storageAccounts/listkeys/Action**.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navegar até BLOBs ou filas no portal

Para exibir dados de BLOB ou de fila no portal, navegue até a **visão geral** da sua conta de armazenamento e clique nos links para **BLOBs** ou **filas**. Como alternativa, você pode navegar até o **serviço blob** e **serviço fila** seções no menu. 

![Navegue até dados de BLOB ou de fila no portal do Azure](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Determinar o método de autenticação atual

Quando você navega para um contêiner ou uma fila, o portal do Azure indica se você está usando a chave de acesso da conta ou sua conta do Azure AD para autenticação.

Os exemplos nesta seção mostram o acesso a um contêiner e a seus BLOBs, mas o portal exibe a mesma mensagem quando você está acessando uma fila e suas mensagens, ou listando filas.

### <a name="authenticate-with-the-account-access-key"></a>Autenticar com a chave de acesso da conta

Se você estiver autenticando usando a chave de acesso da conta, verá a **chave de acesso** especificada como o método de autenticação no Portal:

![Atualmente acessando dados de contêiner com a chave de conta](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Para alternar para o usando a conta do Azure AD, clique no link realçado na imagem. Se você tiver as permissões apropriadas por meio das funções do Azure atribuídas a você, poderá continuar. No entanto, se você não tem as permissões corretas, verá uma mensagem de erro semelhante à seguinte:

![Erro mostrado se a conta do Azure AD não dá suporte ao acesso](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Observe que nenhum blob aparece na lista se sua conta do Azure AD não tem permissões para exibi-los. Clique no link **alternar para a chave de acesso** para usar a tecla de acesso para autenticação novamente.

### <a name="authenticate-with-your-azure-ad-account"></a>Autenticar com sua conta do Azure AD

Se você estiver autenticando usando sua conta do Azure AD, verá a **conta de usuário do Azure ad** especificada como o método de autenticação no Portal:

![Atualmente acessando dados de contêiner com a conta do Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Para alternar para o usando a chave de acesso da conta, clique no link realçado na imagem. Se você tiver acesso à chave de conta, poderá continuar. No entanto, se você não tem acesso à chave de conta, verá uma mensagem de erro semelhante à seguinte:

![Erro mostrado se você não tiver acesso à chave de conta](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Observe que nenhum blob aparecerá na lista se você não tiver acesso às chaves da conta. Clique no link **alternar para a conta de usuário do Azure ad** para usar sua conta do Azure ad para autenticação novamente.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Especificar como autorizar uma operação de upload de BLOB

Ao carregar um blob do portal do Azure, você pode especificar se deseja autenticar e autorizar essa operação com a chave de acesso da conta ou com suas credenciais do Azure AD. Por padrão, o portal usa o método de autenticação atual, conforme mostrado em [determinar o método de autenticação atual](#determine-the-current-authentication-method).

Para especificar como autorizar uma operação de upload de BLOB, siga estas etapas:

1. Na portal do Azure, navegue até o contêiner no qual você deseja carregar um blob.
1. Selecione o botão **Carregar**.
1. Expanda a seção **avançado** para exibir as propriedades avançadas para o blob.
1. No campo **tipo de autenticação** , indique se você deseja autorizar a operação de upload usando sua conta do Azure ad ou com a chave de acesso da conta, conforme mostrado na imagem a seguir:

    :::image type="content" source="media/storage-access-blobs-queues-portal/auth-blob-upload.png" alt-text="Captura de tela mostrando como alterar o método de autorização no carregamento de BLOB":::

## <a name="next-steps"></a>Próximas etapas

- [Autenticar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md)
- [Conceder acesso a contêineres e filas do Azure com RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC usando a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados de blob e fila do Azure com RBAC usando PowerShell](storage-auth-aad-rbac-powershell.md)
