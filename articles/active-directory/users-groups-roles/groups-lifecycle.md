---
title: Definir a expiração de grupos do Office 365 – Azure Active Directory | Microsoft Docs
description: Como configurar o vencimento de grupos do Office 365 no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b551bc8a46a45135bf6a9a8e328b4b0e74f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048258"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configurar a política de expiração de grupos do Office 365

Este artigo diz como gerenciar o ciclo de vida dos grupos do Office 365 definindo uma política de expiração para eles. Você pode definir a política de expiração apenas para grupos do Office 365 no Azure Active Directory (Azure AD).

Depois de configurar a expiração de um grupo:

- Grupos com atividades de usuário são automaticamente renovados à medida que o vencimento se aproxima.
- Os proprietários do grupo são notificados a renovar o grupo, caso o grupo não seja renovado automaticamente.
- Qualquer grupo que não seja renovado é excluído.
- Qualquer grupo do Office 365 que for excluído pode ser restaurado dentro de 30 dias pelos proprietários do grupo ou pelo administrador.

Atualmente, apenas uma política de expiração pode ser configurada para todos os grupos do Office 365 em uma organização AD do Azure.

> [!NOTE]
> A configuração e o uso da política de expiração para grupos do Office 365 exige que você possua, mas não necessariamente, atribua licenças Azure AD Premium para os membros de todos os grupos aos quais a política de expiração é aplicada.

Para saber mais sobre como fazer o download e instalar os cmdlets do PowerShell do Azure AD, confira [PowerShell do Azure Active Directory para Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Renovação automática baseada em atividade

Com a inteligência do Azure AD, os grupos agora são automaticamente renovados com base em se eles foram usados recentemente. Esse recurso elimina a necessidade de ação manual dos proprietários de grupo, porque é baseado na atividade do usuário em grupos em serviços do Office 365, como Outlook, SharePoint ou Teams. Por exemplo, se um proprietário ou um membro do grupo fizer algo como carregar um documento no SharePoint, visitar um canal de Equipes ou enviar um e-mail para o grupo no Outlook, o grupo será automaticamente renovado e o proprietário não recebe nenhuma notificações de renovação.

### <a name="activities-that-automatically-renew-group-expiration"></a>Atividades que renovam automaticamente o vencimento do grupo

As seguintes ações de usuário causam renovação automática do grupo:

- SharePoint: Exibir, editar, baixar, mover, compartilhar ou carregar arquivos
- Outlook: Junte-se a grupo, leia/escreva mensagem de grupo do espaço do grupo, como uma mensagem (no Outlook Web Access)
- Equipes: Visite um canal de equipes

### <a name="auditing-and-reporting"></a>Auditoria e relatórios

Os administradores podem obter uma lista de grupos renovados automaticamente a partir dos logs de auditoria de atividades no Azure AD.

![Renovação automática de grupos com base na atividade](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Funções e permissões

As funções a seguir podem configurar e usar a expiração para grupos do Office 365 no Azure AD.

Função | Permissões
-------- | --------
Administrador global, administrador de grupo ou administrador de usuário | Pode criar, ler, atualizar ou excluir as configurações de política de expiração de grupos do Office 365<br>Pode renovar qualquer grupo do Office 365
Usuário | Pode renovar um grupo do Office 365 que ele possua<br>Pode restaurar um grupo do Office 365 que ele possua<br>Pode ler as configurações da política de expiração

Para saber mais sobre permissões para restaurar um grupo excluído, confira [Restaurar um grupo excluído do Office 365 no Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Definir a expiração de grupo

1. Abra o centro de administração [do Azure AD](https://aad.portal.azure.com) com uma conta que é um administrador global em sua organização Azure AD.

2. Selecione **Grupos**e selecione **Expiração** para abrir as configurações de expiração.
  
   ![Configurações de expiração para grupos](./media/groups-lifecycle/expiration-settings.png)

3. Na página **Expiração,** você pode:

    - Definir o tempo de vida do grupo em dias. Selecionar um dos valores predefinidos ou um valor personalizado (deve ser 31 dias ou mais).
    - Especificar um email para o qual as notificações de vencimento e renovação devem ser enviadas quando um grupo não tem nenhum proprietário.
    - Selecione quais grupos do Office 365 expirarão. Você pode definir a expiração para:
      - **Todos os** Escritório 365 grupos
      - Uma lista de 365 grupos do Escritório **Selecionado**
      - **Nenhum** para restringir a expiração para todos os grupos
    - Salve as configurações quando terminar selecionando **Salvar**.

> [!NOTE]
> - Quando você configura o vencimento pela primeira vez, todos os grupos mais antigos que o intervalo de validade são definidos para 35 dias até o vencimento, a menos que o grupo seja automaticamente renovado ou o proprietário o renove.
> - Quando um grupo dinâmico é excluído e restaurado, ele é visto como um novo grupo e repovoado de acordo com a regra. Esse processo pode levar até 24 horas.
> - Avisos de expiração para grupos usados em Equipes aparecem no feed de Proprietários de Equipes.

## <a name="email-notifications"></a>Notificações por email

Se os grupos não forem renovados automaticamente, notificações de e-mail como esta são enviadas ao Office 365 proprietários do grupo 30 dias, 15 dias e 1 dia antes do vencimento do grupo. O idioma do e-mail é determinado pelo idioma preferido do proprietário de grupos ou pela configuração do idioma Azure AD. Se o proprietário do grupo tiver definido um idioma preferido ou se vários proprietários tiverem o mesmo idioma preferencial, esse idioma será usado. Para todos os outros casos, a configuração do idioma Azure AD é usada.

![Notificações de e-mail de expiração](./media/groups-lifecycle/expiration-notification.png)

A partir do e-mail de notificação **do grupo Renovar,** os proprietários do grupo podem acessar diretamente a página de detalhes do grupo no [Painel de Acesso](https://account.activedirectory.windowsazure.com/r#/applications). Lá, os usuários podem obter mais informações sobre o grupo, como sua descrição, quando foi renovado pela última vez, quando ele expirará, e também a capacidade de renovar o grupo. A página de dados do grupo agora também inclui links para os recursos do grupo do Office 365, para que o proprietário do grupo possivelmente possa visualizar o conteúdo e a atividade em seu grupo.

Quando um grupo expira, o mesmo é excluído um dia depois da data de vencimento. Os proprietários do grupo do Office 365 recebem um email de notificação como este informando sobre o vencimento e exclusão imediata do grupo do Office 365.

![Notificações de e-mail de exclusão de grupo](./media/groups-lifecycle/deletion-notification.png)

Para restaurar o grupo dentro de 30 dias de sua exclusão, selecione **Restaurar grupo** ou use cmdlets do PowerShell, conforme descrito em [Como restaurar um grupo do Office 365 excluído no Azure Active Directory](groups-restore-deleted.md). Observe que o período de restauração de grupo de 30 dias não é personalizável.

Se o grupo que você está restaurando contém documentos, sites do SharePoint ou outros objetos persistentes, pode levar até 24 horas para restaurar completamente o grupo e seu conteúdo.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Como recuperar a data de validade do grupo Office 365

Além do Painel de Acesso, onde os usuários podem visualizar detalhes do grupo, incluindo data de validade e última data renovada, a data de validade de um grupo office 365 pode ser recuperada do Microsoft Graph REST API Beta. expiraçãoDateTime como uma propriedade de grupo foi habilitado no Microsoft Graph Beta. Ele pode ser recuperado com uma solicitação GET. Para obter mais detalhes, consulte [este exemplo](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Para gerenciar as associações de grupos no Painel de Acesso, o "Restringir o acesso a Grupos no Painel de Acesso" precisa ser definido como "Não" na Configuração Geral de Grupos de DiretórioS Ativos do Azure.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Como a expiração do grupo do Office 365 funciona com uma caixa de correio em retenção legal

Quando um grupo expira e é excluído, 30 dias após a exclusão os dados do grupo de aplicativos como Planner, Sites ou Teams são excluídos permanentemente, mas a caixa de correio do grupo que está em retenção legal é mantida e não é excluída permanentemente. O administrador pode usar cmdlets do Exchange para restaurar a caixa de correio para buscar os dados.

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Como a expiração do grupo do Office 365 funciona com a política de retenção

A política de retenção é configurada por meio do Centro de Segurança e Conformidade. Se você configurou uma política de retenção para grupos do Office 365, quando um grupo expira e é excluído, as conversações em grupo na caixa de correio do grupo e nos arquivos no site do grupo são mantidas no recipiente de retenção para o número específico de dias definidos na política de retenção. Os usuários não verão o grupo ou seu conteúdo após a expiração, mas podem recuperar o site e os dados da caixa de correio via descoberta eletrônica.

## <a name="powershell-examples"></a>Exemplos do PowerShell

Aqui estão exemplos de como você pode usar cmdlets PowerShell para configurar as configurações de expiração para grupos do Office 365 em sua organização Azure AD:

1. Instale o módulo PowerShell v2.0 e faça login no prompt do PowerShell:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Configure as configurações de expiração Use o cmdlet New-AzureADMSGroupLifecyclePolicy para definir a vida útil de todos os grupos do Office 365 na organização Azure AD para 365 dias. Notificações de renovação para grupos do Officeemailaddress@contoso.com365 sem proprietários serão enviadas para '
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Recuperar a política existente Get-AzureADMSGroupLifecyclePolicy: este cmdlet recupera as configurações atuais de expiração do grupo do Office 365 que foram configuradas. Neste exemplo, você pode ver:

   - O ID da política
   - A vida útil para todos os grupos do Office 365 na organização Azure AD está definida para 365 dias
   - As notificações de renovação para o Officeemailaddress@contoso.com365 grupos sem proprietários serão enviadas para ' .'
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Atualizar a política existente Set-AzureADMSGroupLifecyclePolicy: este cmdlet é usado para atualizar uma política existente. No exemplo abaixo, o tempo de vida do grupo na política existente é alterado de 365 dias para 180 dias.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Adicionar grupos específicos para a política AzureADMSLifecyclePolicyGroup: esse cmdlet adiciona um grupo à política de ciclo de vida. Por exemplo:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Remover a política existente Remove-AzureADMSGroupLifecyclePolicy: esse cmdlet exclui as configurações de expiração do grupo do Office 365, mas requer o ID da política. Este cmdlet desativa a expiração para grupos office 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Os seguintes cmdlets podem ser usados ​​para configurar a política com mais detalhes. Para obter mais informações, consulte [a documentação do PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem mais informações sobre os grupos do Azure AD.

- [Consultar grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gerenciar configurações de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerenciar membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gerenciar associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerenciar regras dinâmicas para usuários em um grupo](groups-dynamic-membership.md)
