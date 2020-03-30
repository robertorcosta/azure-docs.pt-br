---
title: Restauração em massa de usuários excluídos (pré-visualização) no portal do Azure Active Directory | Microsoft Docs
description: Restaurar usuários excluídos em massa no centro de administrador admin Azure AD no Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174232"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Restauração em massa de usuários excluídos (visualização) no Azure Active Directory

O Azure Active Directory (Azure AD) suporta operações de criação e exclusão em massa de usuários, convite em massa para convidados e suporta listas de download de usuários, grupos e membros do grupo.

## <a name="to-bulk-restore-users"></a>Para restaurar em massa os usuários

1. [Faça login na sua organização Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de usuário na organização Azure AD.
1. No Azure AD, selecione **Usuários Excluídos** > **Deleted**.
1. Na página **Usuários Excluídos,** selecione **Restauração bulk** para carregar um arquivo CSV válido de propriedades dos usuários para restaurar.

   ![Selecione o comando de restauração em massa na página Usuários Excluídos](./media/users-bulk-restore/bulk-restore.png)

1. Abra o arquivo CSV e adicione uma linha para cada usuário que deseja restaurar. O único valor necessário é **ObjectID**. Em seguida, salve o arquivo.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja adicionar](./media/users-bulk-restore/upload-button.png)

1. Na página **Derção em massa (Visualização),** em **Upload seu arquivo csv**, navegue até o arquivo. Quando você selecionar o arquivo e clicar **em Enviar,** a validação do arquivo CSV é iniciada.
1. Quando o conteúdo do arquivo for validado, você verá a mensagem **Arquivo carregado com êxito**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar pela validação, **selecione Enviar** para iniciar a operação em massa do Azure que restaura os usuários.
1. Quando a operação de restauração for concluída, você verá uma notificação de que a operação em massa foi bem sucedida.

Se houver erros, você pode baixar e visualizar o arquivo de resultados na página **de resultados da operação Bulk.** O arquivo contém a razão de cada erro.

## <a name="check-status"></a>Verificar o status

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação Bulk (visualização).**

   ![Verifique o status de upload na página Resultados de Operações em Massa](./media/users-bulk-restore/bulk-center.png)

Em seguida, você pode verificar se os usuários que você restaurou existem na organização Azure AD, seja no portal Azure ou usando o PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Veja usuários restaurados no portal Azure

1. Faça login no centro de administração [do Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de usuário na organização.
1. No painel de navegação, selecione **Azure Active Directory**.
1. Em **Gerenciar**, selecione **Usuários**.
1. Em **Mostrar,** selecione **Todos os usuários** e verifique se os usuários que você restaurou estão listados.

### <a name="view-users-with-powershell"></a>Veja os usuários com o PowerShell

Execute o comando a seguir:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Você deve ver que os usuários que você restaurou estão listados.

## <a name="next-steps"></a>Próximas etapas

- [Usuários de importação a granel](users-bulk-add.md)
- [Usuários de exclusão em massa](users-bulk-delete.md)
- [Lista de downloads de usuários](users-bulk-download.md)
