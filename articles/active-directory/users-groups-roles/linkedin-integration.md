---
title: Consentimento do administrador para conexões de conta do LinkedIn - Azure AD | Microsoft Docs
description: Explica como ativar ou desativar conexões de conta de integração do LinkedIn em aplicativos Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54e3821d269d11397ec4f9f5833e33ac6b555abc
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755110"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integre conexões de conta do LinkedIn no Azure Active Directory

Você pode permitir que os usuários da sua organização acessem suas conexões no LinkedIn em alguns aplicativos da Microsoft. Nenhum dado é compartilhado até que os usuários concordem em conectar suas contas. Você pode integrar sua organização no [centro de administrador](https://aad.portal.azure.com)do Azure Active Directory (Azure AD).

> [!IMPORTANT]
> A configuração de conexões de conta do LinkedIn está sendo distribuída para as organizações AD do Azure. Quando ele é implantado em sua organização, ele é ativado por padrão.
>
> Exceções:
>
> * A configuração não está disponível para clientes que usam o Microsoft Cloud para o governo dos EUA, Microsoft Cloud Alemanha ou Azure e Office 365 operados pela 21Vianet na China.
> * A configuração está desativada por padrão para locatários provisionados na Alemanha. Observe que a configuração não está disponível para clientes que usam o Microsoft Cloud Alemanha.
> * A configuração está desativada por padrão para locatários provisionados na França.
>
> Uma vez que as conexões de conta do LinkedIn são habilitadas para sua organização, as conexões da conta funcionam após o consentimento dos usuários para que os aplicativos acessem os dados da empresa em seu nome. Para obter informações sobre a configuração de consentimento do usuário, consulte [Como remover o acesso de um usuário a um aplicativo](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Habilite conexões de conta do LinkedIn no portal Azure

Você pode habilitar conexões de conta do LinkedIn apenas para os usuários que deseja ter acesso, desde toda a sua organização até apenas usuários selecionados em sua organização.

1. Faça login no centro de administração do [Azure AD](https://aad.portal.azure.com/) com uma conta que é um administrador global para a organização Azure AD.
1. Selecionar **usuários**.
1. Na página **Usuários,** selecione **Configurações do Usuário**.
1. Em **conexões de conta do LinkedIn,** permita que os usuários conectem suas contas para acessar suas conexões no LinkedIn em alguns aplicativos da Microsoft. Nenhum dado é compartilhado até que os usuários concordem em conectar suas contas.

    * Selecione **Sim** para ativar o serviço para todos os usuários da sua organização
    * Selecione **o grupo selecionado** para habilitar o serviço para apenas um grupo de usuários selecionados em sua organização
    * Selecione **Não** para retirar o consentimento de todos os usuários da sua organização

    ![Integre as conexões de conta do LinkedIn na organização](./media/linkedin-integration/linkedin-integration.png)

1. Quando terminar, selecione **Salvar** para salvar suas configurações.

> [!Important]
> A integração do LinkedIn não está totalmente ativada para seus usuários até que eles concordem em conectar suas contas. Nenhum dado é compartilhado quando você habilita conexões de conta para seus usuários.

### <a name="assign-selected-users-with-a-group"></a>Atribuir usuários selecionados com um grupo

Substituímos a opção 'Selecionado' que especifica uma lista de usuários com a opção de selecionar um grupo de usuários para que você possa habilitar a capacidade de conectar contas do LinkedIn e Microsoft para um único grupo em vez de muitos usuários individuais. Se você não tiver conexões de conta do LinkedIn habilitadas para usuários individuais selecionados, você não precisa fazer nada. Se você já habilitou anteriormente as conexões da conta do LinkedIn para usuários individuais selecionados, você deve:

1. Obtenha a lista atual de usuários individuais
1. Mova os usuários individuais habilitados no momento para um grupo
1. Use o grupo do anterior como o grupo selecionado na configuração de conexões de conta do LinkedIn no centro de administração azure AD.

> [!NOTE]
> Mesmo que você não mova seus usuários individuais selecionados atualmente para um grupo, eles ainda podem ver as informações do LinkedIn em aplicativos da Microsoft.

### <a name="move-currently-selected-users-to-a-group"></a>Mover usuários selecionados no momento para um grupo

1. Crie um arquivo CSV dos usuários selecionados para conexões de conta do LinkedIn.
1. Faça login no Microsoft 365 com sua conta administradora.
1. Inicie o PowerShell.
1. Instale o módulo AD do Azure executando`Install-Module AzureAD`
1. Execute o seguinte script:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Para usar o grupo da etapa dois como o grupo selecionado na configuração de conexões de conta do LinkedIn no centro de administração Azure AD, consulte Ativar conexões de [conta do LinkedIn no portal Azure](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Use a Diretiva de Grupo para habilitar conexões de conta do LinkedIn

1. Baixe os [arquivos de modelo administrativo do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extraia os arquivos **ADMX** e copie-os para o repositório central.
1. Gerenciamento de política de grupo aberto.
1. Crie um objeto de diretiva de grupo com a seguinte configuração:**Modelos administrativos** > de **configuração** > do usuário Os recursos**diversos** > **do Microsoft Office 2016** > **Mostram o LinkedIn em aplicativos do Office**.
1. Selecione **Habilitado** ou **Desabilitado**.
  
   Estado | Efeito
   ------ | ------
   **habilitado** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está habilitada. Os usuários da sua organização podem usar os recursos do LinkedIn em seus aplicativos do Office 2016.
   **Desabilitado** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está desabilitada e os usuários finais não podem alterar essa configuração. Os usuários em sua organização não podem usar recursos do LinkedIn nos seus aplicativos do Office 2016.

Essa política de grupo afeta somente os aplicativos do Office 2016 para um computador local. Se os usuários desativarem o LinkedIn em seus aplicativos office 2016, eles ainda podem ver os recursos do LinkedIn no Office 365.

## <a name="next-steps"></a>Próximas etapas

* [Consentimento do usuário e compartilhamento de dados para o LinkedIn](linkedin-user-consent.md)

* [Informações do LinkedIn e recursos em seus aplicativos da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Central de Ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

* [Exibir sua configuração atual de integração do LinkedIn no portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
