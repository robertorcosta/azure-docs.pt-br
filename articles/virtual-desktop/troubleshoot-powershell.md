---
title: Windows desktop virtual PowerShell-Azure
description: Como solucionar problemas com o PowerShell ao configurar um ambiente de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce19c670df5062a11bf86e9c383a322f9033818d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612003"
---
# <a name="windows-virtual-desktop-powershell"></a>PowerShell da Área de Trabalho Virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à atualização do Spring 2020 com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows, a versão 2019 sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md).
>
> A atualização 2020 de área de trabalho virtual do Windows está em visualização pública no momento. Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Use este artigo para resolver erros e problemas ao usar o PowerShell com a área de trabalho virtual do Windows. Para obter mais informações sobre Serviços de Área de Trabalho Remota PowerShell, consulte [Windows Virtual Desktop PowerShell](/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Envie comentários

Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandos do PowerShell usados durante a instalação da área de trabalho virtual do Windows

Esta seção lista os comandos do PowerShell que normalmente são usados durante a configuração da área de trabalho virtual do Windows e fornece maneiras de resolver problemas que podem ocorrer ao usá-los.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Erro: New-AzRoleAssignment: as informações fornecidas não são mapeadas para uma ID de objeto do AD

```powershell
AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

**Causa:** O usuário especificado pelo parâmetro *-SignInName* não pode ser encontrado no Azure Active Directory vinculado ao ambiente de área de trabalho virtual do Windows. 

**Correção:** Certifique-se das ações a seguir.

- O usuário deve ser sincronizado com Azure Active Directory.
- O usuário não deve estar vinculado ao Business-to-Consumer (B2C) ou ao Business-to-Business (B2B) Commerce.
- O ambiente de área de trabalho virtual do Windows deve estar vinculado ao Azure Active Directory correto.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Erro: New-AzRoleAssignment: "o cliente com a ID de objeto não tem autorização para executar a ação sobre o escopo (código: AuthorizationFailed)"

**Causa 1:** A conta que está sendo usada não tem permissões de proprietário na assinatura. 

**Correção 1:** Um usuário com permissões de proprietário precisa executar a atribuição de função. Como alternativa, o usuário precisa ser atribuído à função Administrador de acesso do usuário para atribuir um usuário a um grupo de aplicativos.

**Causa 2:** A conta que está sendo usada tem permissões de proprietário, mas não faz parte do Azure Active Directory do ambiente ou não tem permissões para consultar a Azure Active Directory onde o usuário está localizado.

**Correção 2:** Um usuário com permissões de Active Directory precisa executar a atribuição de função.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Erro: New-AzWvdHostPool--o local não está disponível para o tipo de recurso

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'. 
```

Causa: a área de trabalho virtual do Windows dá suporte à seleção do local de pools de hosts, grupos de aplicativos e espaços de trabalho para armazenar metadados de serviço em determinados locais. Suas opções estão restritas a onde esse recurso está disponível. Esse erro significa que o recurso não está disponível no local escolhido.

Correção: na mensagem de erro, uma lista de regiões com suporte será publicada. Em vez disso, use uma das regiões com suporte.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Erro: New-AzWvdApplicationGroup deve estar no mesmo local que o pool de hosts

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**Causa:** Há uma incompatibilidade de local. Todos os pools de hosts, grupos de aplicativos e espaços de trabalho têm um local para armazenar metadados de serviço. Todos os objetos que você criar que estão associados entre si devem estar no mesmo local. Por exemplo, se um pool de hosts `eastus`estiver em, você também precisará criar os grupos de `eastus`aplicativos no. Se você criar um espaço de trabalho para registrar esses grupos de aplicativos, esse espaço de trabalho `eastus` também precisará estar.

**Correção:** Recupere o local em que o pool de hosts foi criado e, em seguida, atribua o grupo de aplicativos que você está criando para o mesmo local.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da solução de problemas da área de trabalho virtual do Windows e das faixas de escalonamento, consulte [visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante a configuração de seu ambiente de área de trabalho virtual do Windows e pools de hosts, consulte [ambiente e criação de pool de hosts](troubleshoot-set-up-issues.md).
- Para solucionar problemas durante a configuração de uma VM (máquina virtual) na área de trabalho virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [conexões do serviço área de trabalho virtual do Windows](troubleshoot-service-connection.md).
- Para solucionar problemas com clientes Área de Trabalho Remota, consulte [solucionar problemas do cliente área de trabalho remota](troubleshoot-client.md)
- Para saber mais sobre o serviço, consulte [ambiente de área de trabalho virtual do Windows](environment-setup.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](../azure-resource-manager/templates/deployment-history.md).
