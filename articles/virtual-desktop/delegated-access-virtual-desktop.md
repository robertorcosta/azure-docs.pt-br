---
title: Acesso delegado no Windows Virtual Desktop - Azure
description: Como delegar recursos administrativos em uma implantação de Desktop Virtual do Windows, incluindo exemplos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3e27550ecc5b42c2bf0d947690da09e13d88ea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128039"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Acesso delegado na Área de Trabalho Virtual do Windows

O Windows Virtual Desktop tem um modelo de acesso delegado que permite definir a quantidade de acesso que um determinado usuário pode ter atribuindo-lhes uma função. Uma atribuição de função tem três componentes: principal de segurança, definição de função e escopo. O modelo de acesso delegado do Windows Virtual Desktop é baseado no modelo RBAC do Azure. Para saber mais sobre atribuições específicas de função e seus componentes, consulte [a visão geral do controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md).

O acesso delegado do Windows Virtual Desktop suporta os seguintes valores para cada elemento da atribuição da função:

* Entidade de segurança
    * Usuários
    * Entidades de serviço
* Definição de função
    * Funções internas
* Escopo
    * Grupos de inquilinos
    * Locatários
    * Piscinas de hospedagem
    * Grupos de aplicativos

## <a name="built-in-roles"></a>Funções internas

O acesso delegado no Windows Virtual Desktop tem várias definições de função incorporadas que você pode atribuir aos usuários e diretores de serviço.

* Um proprietário rds pode gerenciar tudo, incluindo acesso a recursos.
* Um colaborador RDS pode gerenciar tudo, menos acesso a recursos.
* Um leitor RDS pode ver tudo, mas não pode fazer nenhuma alteração.
* Um operador RDS pode visualizar atividades de diagnóstico.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets PowerShell para atribuições de função

Você pode executar os seguintes cmdlets para criar, visualizar e remover atribuições de função:

* **Get-RdsRoleAssignment** exibe uma lista de atribuições de função.
* **New-RdsRoleAssignment** cria uma nova atribuição de função.
* **Remove-RdsRoleAssignment** exclui atribuições de função.

### <a name="accepted-parameters"></a>Parâmetros aceitos

Você pode modificar os três cmdlets básicos com os seguintes parâmetros:

* **AadTenantId**: especifica o ID de inquilino do Diretório Ativo do Azure do qual o diretor do serviço é membro.
* **AppGroupName**: nome do grupo de aplicativos de desktop remoto.
* **Diagnóstico :** indica o escopo de diagnóstico. (Deve ser emparelhado com os parâmetros **infra-estrutura** ou **inquilino.)**
* **HostPoolName**: nome do pool de host seleto rasteiro da área de trabalho remota.
* **Infraestrutura**: indica o escopo da infra-estrutura.
* **RoleDefinitionName**: nome da função de controle de acesso baseada em função dos Serviços de Área de Trabalho Remota atribuída ao usuário, grupo ou aplicativo. (Por exemplo, proprietário de serviços de desktop remoto, leitor de serviços de desktop remoto e assim por diante.)
* **ServerPrincipleName**: nome do aplicativo Azure Active Directory.
* **SignInName**: o endereço de e-mail do usuário ou o nome principal do usuário.
* **Nome do inquilino**: nome do inquilino da área de trabalho remota.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista mais completa de cmdlets powershell que cada função pode usar, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).

Para obter orientações sobre como configurar um ambiente de desktop virtual do Windows, consulte [o ambiente Windows Virtual Desktop](environment-setup.md).
