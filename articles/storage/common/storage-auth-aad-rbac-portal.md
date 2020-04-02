---
title: Use o portal Azure para atribuir uma função RBAC para acesso a dados
titleSuffix: Azure Storage
description: Saiba como usar o portal Azure para atribuir permissões a um diretor de segurança do Azure Active Directory com controle de acesso baseado em função (RBAC). O Azure Storage suporta funções RBAC incorporadas e personalizadas para autenticação via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d224bd9e9e7b1f8fc9eb45d85e78811d8642fc78
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519553"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Use o portal Azure para atribuir uma função RBAC para acesso a dados de blob e fila

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md). O Azure Storage define um conjunto de funções RBAC incorporadas que abrangem conjuntos comuns de permissões usadas para acessar dados de blob ou fila.

Quando uma função RBAC é atribuída a um diretor de segurança do Azure AD, o Azure concede acesso a esses recursos para esse principal de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Um diretor de segurança do Azure AD pode ser um usuário, um grupo, um diretor de serviço de aplicativo ou uma [identidade gerenciada para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Este artigo descreve como usar o portal Azure para atribuir funções RBAC. O portal Azure fornece uma interface simples para atribuir funções RBAC e gerenciar o acesso aos seus recursos de armazenamento. Você também pode atribuir funções RBAC para recursos de blob e fila usando ferramentas de linha de comando do Azure ou as APIs de gerenciamento de armazenamento do Azure. Para obter mais informações sobre as funções RBAC para recursos de armazenamento, consulte [Authenticate acesso a blobs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções RBAC para blobs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o escopo dos recursos

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções RBAC usando o portal Azure

Depois de determinar o escopo apropriado para uma atribuição de função, navegue até esse recurso no portal Azure. Exiba as configurações **de Controle de acesso (IAM)** para o recurso e siga estas instruções para gerenciar as atribuições da função:

1. Atribua a função RBAC de armazenamento Azure apropriada para conceder acesso a um diretor de segurança Azure AD.

1. Atribua a função Azure Resource Manager [Reader](../../role-based-access-control/built-in-roles.md#reader) aos usuários que precisam acessar contêineres ou filas através do portal Azure usando suas credenciais azure AD. 

As seções a seguir descrevem cada uma dessas etapas com mais detalhes.

> [!NOTE]
> Como proprietário da conta do Armazenamento do Microsoft Azure, você não recebe permissões automaticamente para acessar dados. Você deve atribuir explicitamente a si mesmo uma função RBAC para o Armazenamento do Microsoft Azure. Você pode atribuí-la no nível da sua assinatura, grupo de recursos, conta de armazenamento ou um contêiner ou fila.
>
> Não é possível atribuir uma função escopo a um contêiner ou fila se sua conta de armazenamento tiver um namespace hierárquico ativado.

### <a name="assign-a-built-in-rbac-role"></a>Atribuir uma função RBAC incorporada

Antes de atribuir um papel a um diretor de segurança, certifique-se de considerar o escopo das permissões que você está concedendo. Revise a seção [Determinar escopo de recursos](#determine-resource-scope) para decidir o escopo apropriado.

O procedimento mostrado aqui atribui uma função com escopo a um contêiner, mas você pode seguir as mesmas etapas para atribuir uma função com escopo a uma fila:

1. No [portal Azure,](https://portal.azure.com)vá para sua conta de armazenamento e exiba a **visão geral** da conta.
1. Em Serviços, selecione **Blobs**.
1. Localize o contêiner para o qual você deseja atribuir uma função e exiba as configurações do contêiner.
1. Selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso do contêiner. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.

    ![Captura de tela mostrando as configurações de controle de acesso ao contêiner](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Clique no botão **Adicionar atribuição de função** para adicionar uma nova função.
1. Na janela **Adicionar função de atribuição,** selecione a função Armazenamento Azure que deseja atribuir. Em seguida, procure localizar o diretor de segurança ao qual você deseja atribuir essa função.

    ![Captura de tela que mostra como atribuir uma função do RBAC](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Clique em **Salvar**. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que o usuário adicionado agora tem permissões de leitura para os dados no contêiner nomeado *sample-container*.

    ![Captura de tela mostrando lista de usuários atribuídos a uma função](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Você pode seguir etapas semelhantes para atribuir uma função escopo à conta de armazenamento, grupo de recursos ou assinatura.

### <a name="assign-the-reader-role-for-portal-access"></a>Atribuir a função leitor para acesso ao portal

Quando você atribui uma função incorporada ou personalizada para o Azure Storage a um diretor de segurança, você está concedendo permissões a esse diretor de segurança para executar operações com dados em sua conta de armazenamento. As funções do **Leitor** de dados incorporada fornecem permissões de leitura para os dados em um contêiner ou fila, enquanto as funções incorporadas **do Contribuinte de Dados** fornecem permissões de leitura, gravação e exclusão para um contêiner ou fila. As permissões são escopo do recurso especificado.  
Por exemplo, se você atribuir a função **de contribuinte de dados do Blob de armazenamento** ao usuário Mary no nível de um contêiner chamado **sample-container,** então Mary recebe acesso a todas as bolhas nesse contêiner.

No entanto, se Mary quiser ver uma bolha no portal Azure, então a função **de Contribuinte de Dados blob de armazenamento** por si só não fornecerá permissões suficientes para navegar através do portal até a bolha, a fim de visualizá-lo. Permissões adicionais do Azure AD são necessárias para navegar pelo portal e visualizar os outros recursos que são visíveis lá.

Se seus usuários precisarem ser capazes de acessar blobs no portal Azure, então atribua-lhes uma função RBAC adicional, a função [Reader,](../../role-based-access-control/built-in-roles.md#reader) para aqueles usuários, no nível da conta de armazenamento ou acima. A **função Reader** é uma função do Azure Resource Manager que permite que os usuários visualizem os recursos da conta de armazenamento, mas não os modifiquem. Ele não fornece permissões de leitura para dados no Azure Storage, mas apenas para recursos de gerenciamento de contas.

Siga estas etapas para atribuir a função **Leitor** para que um usuário possa acessar blobs do portal Azure. Neste exemplo, a atribuição é escopo para a conta de armazenamento:

1. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento.
1. Selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso da conta de armazenamento. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
1. Na janela **Adicionar atribuição de função,** selecione a **função 'Leitor'.** 
1. No campo **Atribuir acesso a**, selecione **usuário, grupo ou entidade de serviço do Azure AD**.
1. Procure localizar o diretor de segurança ao qual você deseja atribuir a função.
1. Salve a atribuição de função.

Atribuir a função **Leitor** é necessário apenas para usuários que precisam acessar blobs ou filas usando o portal Azure.

> [!IMPORTANT]
> A versão de visualização do Storage Explorer no portal Azure não suporta o uso de credenciais Azure AD para visualizar e modificar dados de blob ou fila. O Storage Explorer no portal Azure sempre usa as chaves da conta para acessar dados. Para usar o Storage Explorer no portal Azure, você deve ser atribuído a uma função que inclua **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre as funções RBAC para recursos de armazenamento, consulte [Authenticate acesso a blobs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md). 
- Para saber mais sobre o RBAC, consulte [O que é o Controle de Acesso Baseado em Função (RBAC)?](../../role-based-access-control/overview.md).
- Para saber como atribuir e gerenciar atribuições de função do RBAC com Azure PowerShell, CLI do Azure ou API REST, consulte estes artigos:
    - [Gerenciar o controle de acesso baseado em função (RBAC) com o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a CLI do Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a API REST](../../role-based-access-control/role-assignments-rest.md)
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Usar o Azure AD com aplicativos do Armazenamento do Microsoft Azure](storage-auth-aad-app.md).
