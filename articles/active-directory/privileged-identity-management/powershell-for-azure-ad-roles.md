---
title: PowerShell para funções azure AD em PIM - Azure AD | Microsoft Docs
description: Gerencie funções Ad do Azure usando cmdlets PowerShell no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 364b0268be99685b18db175ffbabd9350531d500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048883"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell para funções ad do Azure em gerenciamento de identidade privilegiado

Este artigo contém instruções para usar os cmdlets PowerShell do Azure Active Directory (Azure AD) para gerenciar funções Azure AD no Privileged Identity Management (PIM). Ele também explica como configurar usando o módulo PowerShell do Azure AD.

> [!Note]
> Nosso PowerShell oficial só é suportado se você estiver na nova versão do Azure AD Privileged Identity Management. Por favor, vá para o Gerenciamento de Identidade Privilegiada e certifique-se de que você tem o seguinte banner na lâmina de partida rápida.
> [![verificar a versão do Gerenciamento de Identidade Privilegiada que você tem](media/pim-how-to-add-role-to-user/pim-new-version.png "Selecione o Azure AD > gerenciamento de identidade privilegiado")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Se você não tem este banner, por favor, aguarde como estamos no processo de implantação desta experiência atualizada ao longo das próximas semanas.
> Os cmdlets PowerShell de gerenciamento de identidade privilegiado são suportados através do módulo Azure AD Preview. Se você estiver usando um módulo diferente e esse módulo estiver retornando uma mensagem de erro, comece a usar este novo módulo. Se você tem algum sistema de produção construído em cima de um módulo diferente, por favor, entre em contato compim_preview@microsoft.com

## <a name="installation-and-setup"></a>Instalação e configuração

1. Instale o módulo de visualização ad do Azure

        Install-module AzureADPreview

1. Certifique-se de que você tem as permissões de função necessárias antes de prosseguir. Se você estiver tentando executar tarefas de gerenciamento, como dar uma atribuição de função ou atualizar a configuração da função, certifique-se de que você tenha a função de administrador global ou de função privilegiada. Se você está apenas tentando ativar sua própria atribuição, não são necessárias permissões além das permissões de usuário padrão.

1. Conecte-se ao Azure AD.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. Encontre seu ID de inquilino indo para **o Azure Active Directory** > **Properties** > **ID**. Na seção cmdlets, use este ID sempre que precisar para fornecer o recursoId.

    ![Encontre o ID do inquilino nas propriedades da organização Azure AD](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> As seções a seguir são exemplos simples que podem ajudar a colocá-lo em funcionamento. Você pode encontrar documentação mais detalhada sobre https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_managementos seguintes cmdlets em . No entanto, você precisará substituir "azureResources" no parâmetro providerID por "aadRoles". Você também precisará lembrar de usar o ID do inquilino para sua organização Azure AD como parâmetro de recursoId.

## <a name="retrieving-role-definitions"></a>Recuperando definições de função

Use o cmdlet a seguir para obter todas as funções ad incorporadas e personalizadas do Azure em sua organização Azure AD (inquilino). Este passo importante fornece o mapeamento entre o nome da função e o roleDefinitionId. O roleDefinitionId é usado em todos esses cmdlets para referenciar uma função específica.

O roleDefinitionId é específico para a sua organização Azure AD e é diferente da funçãoDefinitionId retornada pela API de gerenciamento de função.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Resultado:

![Obtenha todos os papéis para a organização Azure AD](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Recuperando atribuições de função

Use o cmdlet a seguir para recuperar todas as atribuições de função em sua organização Azure AD.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

Use o cmdlet a seguir para recuperar todas as atribuições de função para um usuário em particular. Esta lista também é conhecida como "Minhas Funções" no portal Azure AD. A única diferença aqui é que você adicionou um filtro para o ID do assunto. O ID de assunto neste contexto é o ID do usuário ou o ID do grupo.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

Use o cmdlet a seguir para recuperar todas as atribuições de função para uma determinada função. O roleDefinitionId aqui é o ID que é devolvido pelo cmdlet anterior.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

Os cmdlets resultam em uma lista de objetos de atribuição de função mostrados abaixo. O ID do assunto é o ID do usuário a quem a função é atribuída. O estado de atribuição pode ser ativo ou elegível. Se o usuário estiver ativo e houver um ID no campo LinkedEligibleRoleAssignmentId, isso significa que a função está ativada no momento.

Resultado:

![Recupere todas as atribuições de função para a organização Azure AD](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Atribuir uma função

Use o cmdlet a seguir para criar uma atribuição elegível.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

O cronograma, que define a hora de início e término da atribuição, é um objeto que pode ser criado como o seguinte exemplo:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"

## <a name="activate-a-role-assignment"></a>Ativar uma atribuição de função

Use o cmdlet a seguir para ativar uma atribuição elegível.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

Este cmdlet é quase idêntico ao cmdlet para criar uma atribuição de função. A principal diferença entre os cmdlets é que, para o parâmetro –Tipo, a ativação é "userAdd" em vez de "adminAdd". A outra diferença é que o parâmetro AssignmentState é "Ativo" em vez de "Elegível".

> [!Note]
> Existem dois cenários limitantes para ativação de função através do PowerShell.
> 1. Se você precisar do sistema de bilhetes / número do bilhete na sua configuração de função, não há como fornecê-los como parâmetro. Assim, não seria possível ativar o papel além do portal Azure. Esse recurso será lançado para a PowerShell nos próximos meses.
> 1. Se você precisar de autenticação multifatorial para ativação de funções, atualmente não há como o PowerShell desafiar o usuário quando ativar sua função. Em vez disso, os usuários precisarão acionar o desafio do MFA quando se conectarem ao Azure AD, seguindo [este post](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) de blog de um de nossos engenheiros. Se você está desenvolvendo um aplicativo para PIM, uma implementação possível é desafiar os usuários e reconectá-los ao módulo depois que eles recebem um erro "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Recuperando e atualizando configurações de função

Use o cmdlet a seguir para obter todas as configurações de função em sua organização Azure AD.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

Há quatro objetos principais na configuração. Apenas três desses objetos são usados atualmente pelo PIM. As Configurações de Membro do Usuário são configurações de ativação, AdminEligibleSettings são configurações de atribuição para atribuições elegíveis e as Configurações de adminmembersão são configurações de atribuição para atribuições ativas.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Para atualizar a configuração de função, você precisará primeiro definir um objeto de configuração da seguinte forma:

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

Em seguida, você pode ir em frente e aplicar a configuração a um dos objetos para uma função específica, como mostrado abaixo. O ID aqui é a configuração de função ID que pode ser recuperada a partir do resultado das configurações de função de lista cmdlet.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função no Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
