---
title: Windows Virtual Desktop PowerShell - Azure
description: Como solucionar problemas com o PowerShell quando você configura um ambiente de inquilino do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fb5436c2b5c30c5336385792d0597bdcea2b538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127474"
---
# <a name="windows-virtual-desktop-powershell"></a>PowerShell da Área de Trabalho Virtual do Windows

Use este artigo para resolver erros e problemas ao usar o PowerShell com o Windows Virtual Desktop. Para obter mais informações sobre o PowerShell dos Serviços de Área De desktop remota, consulte [o Windows Virtual Desktop Powershell](/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Fornecer comentários

Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandos PowerShell usados durante a configuração do Windows Virtual Desktop

Esta seção lista os comandos PowerShell que normalmente são usados durante a configuração do Windows Virtual Desktop e fornece maneiras de resolver problemas que podem ocorrer ao usá-los.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Erro: Comando Add-RdsAppGroupUser -- O UserPrincipalName especificado já está atribuído a um grupo de aplicativos do RemoteApp no pool de host especificado

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Causa:** O nome de usuário usado já foi atribuído a um grupo de aplicativos de um tipo diferente. Os usuários não podem ser atribuídos a um grupo remoto de desktop e aplicativo remoto o mesmo pool de host de sessão.

**Correção:** Se o usuário precisar de aplicativos remotos e desktop remoto, crie diferentes pools de host ou conceda acesso ao usuário à área de trabalho remota, o que permitirá o uso de qualquer aplicativo na VM do host de sessão.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Erro: Comando Add-RdsAppGroupUser -- O UserPrincipalName especificado não existe no Diretório Ativo do Azure associado ao inquilino da área de trabalho remota

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Causa:** O usuário especificado pelo -UserPrincipalName não pode ser encontrado no Azure Active Directory vinculado ao inquilino do Windows Virtual Desktop.

**Correção:** Confirme os itens na lista a seguir.

- O usuário é sincronizado com o Azure Active Directory.
- O usuário não está vinculado ao comércio de negócios para consumidor (B2C) ou de negócios para empresas (B2B).
- O inquilino do Windows Virtual Desktop está vinculado para corrigir o Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities -- O usuário não está autorizado a consultar o serviço de gerenciamento

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Causa:** -Parâmetro tenantname

**Correção:** Problema Get-RdsDiagnosticActivities com \<-TenantName TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities -- o usuário não está autorizado a consultar o serviço de gerenciamento

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Causa:** Usando o interruptor de implantação.

**Correção:** -O switch de implantação só pode ser usado pelos administradores de implantação. Esses administradores geralmente são membros da equipe de Serviços de Desktop Remoto/Windows Virtual Desktop. Substitua o switch -Deployment \<por -TenantName TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: New-RdsRoleAssignment -- o usuário não está autorizado a consultar o serviço de gerenciamento

**Causa 1:** A conta que está sendo usada não tem permissões do proprietário de serviços de área de trabalho remota no inquilino.

**Correção 1:** Um usuário com permissões de proprietário de serviços de desktop remoto precisa executar a atribuição da função.

**Causa 2:** A conta que está sendo usada tem permissões do proprietário do Remote Desktop Services, mas não faz parte do Azure Active Directory do inquilino ou não tem permissões para consultar o Azure Active Directory onde o usuário está localizado.

**Correção 2:** Um usuário com permissões de Diretório Ativo precisa executar a atribuição da função.

>[!Note]
>O New-RdsRoleAssignment não pode dar permissões a um usuário que não existe no Azure Active Directory (AD).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral sobre a solução de problemas do Windows Virtual Desktop e as faixas de escalonamento, consulte [Visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de solução de problemas.
- Para solucionar problemas ao criar um inquilino e um pool de host em um ambiente de Desktop Virtual do Windows, consulte [Criação de inquilino e pool de host](troubleshoot-set-up-issues.md).
- Para solucionar problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte [Configuração da máquina virtual do host session](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de clientes do Windows Virtual Desktop, consulte [conexões de serviço do Windows Virtual Desktop](troubleshoot-service-connection.md).
- Para solucionar problemas com clientes de desktop remoto, consulte [Solucionar problemas do cliente de desktop remoto](troubleshoot-client.md)
- Para saber mais sobre o serviço, consulte o [ambiente Windows Virtual Desktop](environment-setup.md).
- Para passar por um tutorial de solução de problemas, consulte Tutorial: Implantação de [modelo do Gerenciador de recursos de solução de problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](../azure-resource-manager/templates/deployment-history.md).
