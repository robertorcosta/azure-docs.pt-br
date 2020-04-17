---
title: Exclua em massa os usuários no portal do Azure Active Directory | Microsoft Docs
description: Excluir usuários em massa no centro de administrador esporão do Azure no Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: beb8b4f35dc5f02e59cced05a6bcfc235d42f996
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532792"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Exclua em massa os usuários no Azure Active Directory

Usando o portal Azure Active Directory (Azure AD), você pode remover um grande número de membros para um grupo usando um arquivo CSV (Comma-Separated Values, valores separados por comuma) para excluir em massa os usuários.

## <a name="to-bulk-delete-users"></a>Para excluir em massa os usuários

1. [Faça login na sua organização Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de usuário na organização.
1. No Azure AD, selecione **'Usuários** > **Em massa excluir**.
1. Na página **de exclusão em massa do usuário,** selecione **Baixar** para receber um arquivo CSV válido de propriedades do usuário.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja excluir](./media/users-bulk-delete/bulk-delete.png)

1. Abra o arquivo CSV e adicione uma linha para cada usuário que você deseja excluir. O único valor necessário é **o nome principal do usuário.** Em seguida, salve o arquivo.

   ![O arquivo CSV contém nomes e IDs dos usuários para excluir](./media/users-bulk-delete/delete-csv-file.png)

1. Na **página de exclusão em massa** do usuário, em **Upload seu arquivo csv**, navegue até o arquivo. Quando você seleciona o arquivo e clica em enviar, a validação do arquivo CSV é iniciada.
1. Quando o conteúdo do arquivo for validado, você verá a mensagem **Arquivo carregado com êxito**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar pela validação, **selecione Enviar** para iniciar a operação em massa do Azure que exclui os usuários.
1. Quando a operação de exclusão for concluída, você verá uma notificação de que a operação em massa foi bem sucedida.

Se houver erros, você pode baixar e visualizar o arquivo de resultados na página **de resultados da operação Bulk.** O arquivo contém a razão de cada erro.

## <a name="check-status"></a>Verificar o status

Você pode ver o status de todas as suas solicitações em massa pendentes na página de resultados da **operação Bulk.**

   [![](media/users-bulk-delete/bulk-center.png "Check delete status in the Bulk Operations Results page")](media/users-bulk-delete/bulk-center.png#lightbox)

Em seguida, você pode verificar se os usuários que você excluiu existem na organização Azure AD, seja no portal Azure ou usando o PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verifique usuários excluídos no portal Azure

1. Entre no portal do Azure com uma conta que seja um Administrador de usuários na organização.
1. No painel de navegação, selecione **Azure Active Directory**.
1. Em **Gerenciar**, selecione **Usuários**.
1. Em **Mostrar,** selecione **Apenas todos os usuários** e verifique se os usuários excluídos não estão mais listados.

### <a name="verify-deleted-users-with-powershell"></a>Verifique usuários excluídos com o PowerShell

Execute o comando a seguir:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Verifique se os usuários que você excluiu não estão mais listados.

## <a name="next-steps"></a>Próximas etapas

- [Adição de Usuários em Massa](users-bulk-add.md)
- [Lista de downloads de usuários](users-bulk-download.md)
- [Usuários de restauração em massa](users-bulk-restore.md)
