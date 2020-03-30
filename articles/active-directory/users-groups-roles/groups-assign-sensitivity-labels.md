---
title: Atribuir rótulos de sensibilidade a grupos - Azure AD | Microsoft Docs
description: Como criar regras de associação para preencher automaticamente os grupos e uma referência de regra.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 667fb39aabfec14cff01221b82a45ba8ad1d68d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329725"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Atribuir rótulos de sensibilidade aos grupos do Office 365 no Azure Active Directory (visualização)

O Azure Active Directory (Azure AD) suporta a aplicação de rótulos de sensibilidade publicados pelo [centro de conformidade Microsoft 365](https://sip.protection.office.com/homepage) aos grupos do Office 365. Os rótulos de sensibilidade se aplicam ao grupo em serviços como Outlook, Microsoft Teams e SharePoint. Esse recurso está atualmente em visualização pública. Para obter mais informações sobre o suporte a aplicativos do Office 365, consulte [o suporte ao Office 365 para etiquetas de sensibilidade](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels).

> [!IMPORTANT]
> Para configurar esse recurso, deve haver pelo menos uma licença Ativa Do Azure Active Directory Premium P1 em sua organização Azure AD.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Habilite o suporte ao rótulo de sensibilidade no PowerShell

Para aplicar rótulos publicados em grupos, primeiro você deve habilitar o recurso. Essas etapas habilitam o recurso no Azure AD.

1. Abra uma janela do Windows PowerShell em seu computador. Você pode abri-lo sem privilégios elevados.
1. Execute os comandos a seguir para preparar para executar os cmdlets.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Na **página Entrar na página da sua conta,** digite sua conta de administração e senha para conectá-lo ao seu serviço e selecione **Entrar .**
1. Busque as configurações atuais do grupo para a organização Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Se nenhuma configuração de grupo foi criada para esta organização Azure AD, você deve primeiro criar as configurações. Siga as etapas em [cmdlets do Azure Active Directory para configurar configurações de grupo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) para criar configurações de grupo para esta organização Azure AD.

1. Em seguida, exiba as configurações atuais do grupo.

    ```PowerShell
    $Setting.Values
    ```

1. Em seguida, habilite o recurso:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Em seguida, salve as alterações e aplique as configurações:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

É isso. Você habilitou o recurso e pode aplicar rótulos publicados em grupos.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Atribuir um rótulo a um novo grupo no portal Azure

1. Faça login no [centro de administrador esporáscreis do Azure](https://aad.portal.azure.com).
1. Selecione **Grupos**e selecione **Novo grupo**.
1. Na página **Do Novo Grupo,** selecione **Office 365**e preencha as informações necessárias para o novo grupo e selecione um rótulo de sensibilidade da lista.

   ![Atribuir um rótulo de sensibilidade na página Novos grupos](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Salve suas alterações e selecione **Criar**.

Seu grupo é criado e as configurações de site e grupo associadas ao rótulo selecionado são automaticamente aplicadas.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Atribuir um rótulo a um grupo existente no portal Azure

1. Faça login no centro de administração do [Azure AD](https://aad.portal.azure.com) com uma conta administradora de grupos ou como proprietário do grupo.
1. Selecione **Grupos**.
1. Na página **Todos os grupos,** selecione o grupo que deseja rotular.
1. Na página do grupo selecionado, selecione **Propriedades** e selecione um rótulo de sensibilidade na lista.

   ![Atribuir um rótulo de sensibilidade na página de visão geral de um grupo](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Selecione **Salvar** para salvar suas alterações.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Remova um rótulo de um grupo existente no portal Azure

1. Faça login no [centro de administração azure AD](https://aad.portal.azure.com) com uma conta de administração global ou de grupos, ou como proprietário do grupo.
1. Selecione **Grupos**.
1. Na página **Todos os grupos,** selecione o grupo do que deseja remover o rótulo.
1. Na página **Grupo,** selecione **Propriedades**.
1. Selecione **Remover**.
1. Selecione **Salvar** para salvar suas alterações.

## <a name="using-classic-azure-ad-classifications"></a>Usando classificações clássicas do Azure AD

Depois de habilitar esse recurso, as classificações "clássicas" para grupos aparecerão apenas em grupos e sites existentes, e você deve usá-los para novos grupos apenas se criar grupos em aplicativos que não suportam rótulos de sensibilidade. Seu admin pode convertê-los em rótulos de sensibilidade mais tarde, se necessário. Classificações clássicas são as classificações antigas que `ClassificationList` você configura definindo valores para a configuração no Azure AD PowerShell. Quando esse recurso estiver ativado, essas classificações não serão aplicadas a grupos.

## <a name="troubleshooting-issues"></a>Solução de problemas

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Rótulos de sensibilidade não estão disponíveis para atribuição em um grupo

A opção de etiqueta de sensibilidade só é exibida para grupos quando todas as seguintes condições são atendidas:

1. Os rótulos são publicados no Microsoft 365 Compliance Center para este inquilino.
1. O recurso está ativado, EnableMIPLabels está definido como True no PowerShell.
1. O grupo é um grupo do Office 365.
1. O inquilino tem uma licença Ativa do Azure Active Directory Premium P1.
1. O usuário de login atual tem privilégios suficientes para atribuir rótulos. O usuário deve ser um administrador global, administrador de grupo ou o proprietário do grupo.

Certifique-se de que todas as condições sejam atendidas para atribuir rótulos a um grupo.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>O rótulo que eu quero atribuir não está na lista

Se o rótulo que você está procurando não está na lista, este pode ser o caso por uma das seguintes razões:

- O rótulo pode não ser publicado no Microsoft 365 Compliance Center. Isso também pode se aplicar a rótulos que não são mais publicados. Por favor, verifique com o administrador para obter mais informações.
- O rótulo pode ser publicado, no entanto, não está disponível para o usuário que está conectado. Por favor, verifique com o administrador mais informações sobre como obter acesso ao rótulo.

### <a name="how-to-change-the-label-on-a-group"></a>Como mudar o rótulo em um grupo

Os rótulos podem ser trocados a qualquer momento usando as mesmas etapas que atribuir um rótulo a um grupo existente, da seguinte forma:

1. Faça login no [centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta administradora Global ou Group ou como proprietário do grupo.
1. Selecione **Grupos**.
1. Na página **Todos os grupos,** selecione o grupo que deseja rotular.
1. Na página do grupo selecionado, selecione **Propriedades** e selecione um novo rótulo de sensibilidade na lista.
1. Selecione **Salvar**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>As alterações de configuração de grupo para rótulos publicados não são atualizadas nos grupos

Como uma prática recomendada, não recomendamos que você altere as configurações de grupo para um rótulo depois que o rótulo é aplicado a grupos. Quando você faz alterações nas configurações de grupo associadas aos rótulos publicados no [centro de conformidade do Microsoft 365,](https://sip.protection.office.com/homepage)essas alterações de diretiva não são aplicadas automaticamente nos grupos impactados.

Se você tiver que fazer uma alteração, use [um script Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) para aplicar manualmente atualizações aos grupos impactados. Este método garante que todos os grupos existentes impontem a nova configuração.

## <a name="next-steps"></a>Próximas etapas

- [Use etiquetas de sensibilidade com microsoft teams, grupos do Office 365 e sites SharePoint](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Atualizar grupos após a alteração manual da política de rótulos com o script Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Editar as configurações de grupo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Gerenciar grupos usando comandos do PowerShell](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
