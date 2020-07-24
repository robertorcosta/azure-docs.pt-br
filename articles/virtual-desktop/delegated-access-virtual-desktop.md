---
title: Acesso delegado na área de trabalho virtual do Windows Spring 2020 – Azure
description: Como delegar recursos administrativos em uma implantação de área de trabalho virtual do Windows, incluindo exemplos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 92de6011f85079e9207ab9f1a5bd8c3a045823d7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082691"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Acesso delegado na Área de Trabalho Virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à atualização da Spring 2020 com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows na versão 2019, sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).
>
> A atualização 2020 da Área de Trabalho Virtual do Windows está em versão prévia pública no momento. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A área de trabalho virtual do Windows tem um modelo de acesso delegado que permite definir a quantidade de acesso que um usuário específico tem permissão para ter atribuindo a eles uma função. Uma atribuição de função tem três componentes: entidade de segurança, definição de função e escopo. O modelo de acesso delegado da área de trabalho virtual do Windows baseia-se no modelo RBAC do Azure. Para saber mais sobre atribuições de função específicas e seus componentes, consulte [a visão geral do controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md).

O acesso delegado da área de trabalho virtual do Windows oferece suporte aos seguintes valores para cada elemento da atribuição de função:

* Entidade de segurança
    * Usuários
    * Grupos de usuários
    * Entidades de serviço
* Definição de função
    * Funções internas
    * Funções personalizadas
* Escopo
    * Pools de hosts
    * Grupos de aplicativos
    * Workspaces

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets do PowerShell para atribuições de função

Antes de começar, certifique-se de seguir as instruções em [Configurar o módulo do PowerShell](powershell-module.md) para configurar o módulo do PowerShell da área de trabalho virtual do Windows, caso ainda não tenha feito isso.

A área de trabalho virtual do Windows usa o RBAC (controle de acesso baseado em função) do Azure ao publicar grupos de aplicativos para usuários ou grupos de usuários. A função de usuário de virtualização de desktop é atribuída ao usuário ou grupo de usuários e o escopo é o grupo de aplicativos. Essa função dá ao usuário acesso especial a dados no grupo de aplicativos.  

Execute o seguinte cmdlet para adicionar Azure Active Directory usuários a um grupo de aplicativos:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'  
```

Execute o seguinte cmdlet para adicionar Azure Active Directory grupo de usuários a um grupo de aplicativos:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista mais completa de cmdlets do PowerShell que cada função pode usar, consulte a [referência do PowerShell](/powershell/windows-virtual-desktop/overview).

Para obter uma lista completa de funções com suporte no RBAC do Azure, confira [funções internas do Azure](../role-based-access-control/built-in-roles.md).

Para obter diretrizes sobre como configurar um ambiente de área de trabalho virtual do Windows, consulte [ambiente de área de trabalho virtual do Windows](environment-setup.md).
