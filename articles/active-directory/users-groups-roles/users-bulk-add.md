---
title: Usuários de criação em massa (pré-visualização) no portal do Azure Active Directory | Microsoft Docs
description: Adicionar usuários em massa no centro de administrador esporão Azure AD no Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174344"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Usuários de criação em massa (visualização) no Azure Active Directory

O Azure Active Directory (Azure AD) suporta operações de criação e exclusão em massa de usuários, convite em massa para convidados e suporta listas de download de usuários, grupos e membros do grupo.

## <a name="required-permissions"></a>Permissões necessárias

Para criar em massa usuários no portal de administração, você deve ser conectado como administrador global ou administrador de usuário.

## <a name="to-create-users-in-bulk"></a>Para criar usuários em massa

1. [Faça login na sua organização Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de usuário na organização.
1. No Azure AD, selecione **User** > **Bulk create**.
1. Na página **de usuário criar em massa,** selecione **Baixar** para receber um arquivo csv (valores separados por comma) válidos de propriedades do usuário e, em seguida, adicione os usuários que deseja criar.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja adicionar](./media/users-bulk-add/upload-button.png)

1. Abra o arquivo CSV e adicione uma linha para cada usuário que deseja criar. Os únicos valores necessários são **Nome,** **Nome principal do usuário,** **senha inicial** e login de **bloco (Sim/Não)**. Em seguida, salve o arquivo.

   ![O arquivo CSV contém nomes e IDs dos usuários para criar](./media/users-bulk-add/add-csv-file.png)

1. Na página **Bulk create user (Preview),** em Upload seu arquivo CSV, navegue até o arquivo. Quando você selecionar o arquivo e clicar **em Enviar,** a validação do arquivo CSV é iniciada.
1. Depois que o conteúdo do arquivo for validado, você verá **O arquivo carregado com sucesso**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o seu arquivo passar pela validação, **selecione Enviar** para iniciar a operação em massa do Azure que importa os novos usuários.
1. Quando a operação de importação for concluída, você verá uma notificação do status do trabalho de operação em massa.

Se houver erros, você pode baixar e visualizar o arquivo de resultados na página **de resultados da operação Bulk.** O arquivo contém a razão de cada erro.

## <a name="check-status"></a>Verificar o status

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação Bulk (visualização).**

   ![Verifique o status de upload na página Resultados de Operações em Massa](./media/users-bulk-add/bulk-center.png)

Em seguida, você pode verificar se os usuários que você criou existem na organização Azure AD, seja no portal Azure ou usando o PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verifique os usuários no portal Azure

1. Faça login no centro de administração [do Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de usuário na organização.
1. No painel de navegação, selecione **Azure Active Directory**.
1. Em **Gerenciar**, selecione **Usuários**.
1. Em **Mostrar,** selecione **Todos os usuários** e verifique se os usuários criados estão listados.

### <a name="verify-users-with-powershell"></a>Verifique os usuários com o PowerShell

Execute o comando a seguir:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Você deve ver que os usuários que você criou estão listados.

## <a name="bulk-import-service-limits"></a>Limites de serviço de importação em massa

Cada atividade em massa para criar usuários pode ser executada por até uma hora. Isso permite a criação em massa de pelo menos 50.000 usuários.

## <a name="next-steps"></a>Próximas etapas

- [Usuários de exclusão em massa](users-bulk-delete.md)
- [Lista de downloads de usuários](users-bulk-download.md)
- [Usuários de restauração em massa](users-bulk-restore.md)
