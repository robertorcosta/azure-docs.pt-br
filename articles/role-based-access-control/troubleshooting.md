---
title: Solução de problemas com o RBAC para recursos do Azure | Microsoft Docs
description: Solucione problemas com RBAC (controle de acesso baseado em função) para recursos do Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 2351e6a63723156cce646a6a1cdda837b18a8f91
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456839"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Solução de problemas com o RBAC para recursos do Azure

Este artigo responde a perguntas comuns sobre o RBAC (controle de acesso baseado em função) para recursos do Azure, para que você saiba o que esperar ao usar as funções no portal do Azure e possa solucionar problemas de acesso.

## <a name="problems-with-rbac-role-assignments"></a>Problemas com as atribuições de função RBAC

- If you are unable to add a role assignment in the Azure portal on **Access control (IAM)** because the **Add** > **Add role assignment** option is disabled or because you get the permissions error "The client with object id does not have authorization to perform action", check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Authorization/roleAssignments/write` permission such as [Owner](built-in-roles.md#owner) or [User Access Administrator](built-in-roles.md#user-access-administrator) at the scope you are trying to assign the role.
- If you get the error message "No more role assignments can be created (code: RoleAssignmentLimitExceeded)" when you try to assign a role, try to reduce the number of role assignments by assigning roles to groups instead. O Azure dá suporte a até **2.000** atribuições de função por assinatura. This role assignments limit is fixed and cannot be increased.

## <a name="problems-with-custom-roles"></a>Problemas com funções personalizadas

- If you need steps for how to create a custom role, see the custom role tutorials using [Azure PowerShell](tutorial-custom-role-powershell.md) or [Azure CLI](tutorial-custom-role-cli.md).
- If you are unable to update an existing custom role, check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Authorization/roleDefinition/write` permission such as [Owner](built-in-roles.md#owner) or [User Access Administrator](built-in-roles.md#user-access-administrator).
- If you are unable to delete a custom role and get the error message "There are existing role assignments referencing role (code: RoleDefinitionHasAssignments)", then there are role assignments still using the custom role. Remova essas atribuições de função e tente excluir a função personalizada novamente.
- Se você receber a mensagem de erro "Limite de definição de função excedido. No more role definitions can be created (code: RoleDefinitionLimitExceeded)" when you try to create a new custom role, delete any custom roles that aren't being used. Azure supports up to **5000** custom roles in a tenant. (Para nuvens especializados, como o Azure Governamental, Azure Alemanha e Azure China 21Vianet, o limite é de 2000 funções personalizadas.)
- If you get an error similar to "The client has permission to perform action 'Microsoft.Authorization/roleDefinitions/write' on scope '/subscriptions/{subscriptionid}', however the linked subscription was not found" when you try to update a custom role, check whether one or more [assignable scopes](role-definitions.md#assignablescopes) have been deleted in the tenant. Se o escopo tiver sido excluído, crie um tíquete de suporte, pois não há nenhuma solução de autoatendimento disponível no momento.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Recuperar RBAC quando as assinaturas são movidas entre locatários

- If you need steps for how to transfer a subscription to a different Azure AD tenant, see [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md).
- Quando você transfere uma assinatura a outro locatário do AAD, todas as atribuições de função são excluídas permanentemente do locatário de origem e não são migradas para o locatário de destino. Você precisa recriar as atribuições de função no locatário de destino. You also have to manually recreate managed identities for Azure resources. For more information, see [FAQs and known issues with managed identities](../active-directory/managed-identities-azure-resources/known-issues.md).
- If you are an Azure AD Global Administrator and you don't have access to a subscription after it was moved between tenants, use the **Access management for Azure resources** toggle to temporarily [elevate your access](elevate-access-global-admin.md) to get access to the subscription.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemas com os administradores de serviço ou coadministradores

- If you are having issues with Service administrator or Co-administrators, see [Add or change Azure subscription administrators](../billing/billing-add-change-azure-subscription-administrator.md) and [Classic subscription administrator roles, Azure RBAC roles, and Azure AD administrator roles](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Access denied or permission errors

- If you get the permissions error "The client with object id does not have authorization to perform action over scope (code: AuthorizationFailed)" when you try to create a resource, check that you are currently signed in with a user that is assigned a role that has write permission to the resource at the selected scope. Por exemplo, para gerenciar máquinas virtuais em um grupo de recursos, você deverá ter a função [Colaborador da Máquina Virtual](built-in-roles.md#virtual-machine-contributor) no grupo de recursos (ou escopo pai). Para obter uma lista das permissões de cada função interna, confira [Funções internas para recursos do Azure](built-in-roles.md).
- If you get the permissions error "You don't have permission to create a support request" when you try to create or update a support ticket, check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Support/supportTickets/write` permission, such as [Support Request Contributor](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Role assignments with Unknown security principal

If you assign a role to a security principal (user, group, service principal, or managed identity) and then you later delete that security principal without removing the role assignment, the security principal type for the role assignment will be listed as **Unknown**. The following screenshot shows an example in the Azure portal. The security principal name is listed as **Identity deleted** and **Identity no longer exists**. 

![Grupo de recursos do aplicativo Web](./media/troubleshooting/unknown-security-principal.png)

If you list this role assignment using Azure PowerShell, you will see an empty `DisplayName` and an `ObjectType` set to Unknown. For example, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) returns a role assignment that is similar to the following:

```azurepowershell
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Similarly, if you list this role assignment using Azure CLI, you will see an empty `principalName`. For example, [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) returns a role assignment that is similar to the following:

```azurecli
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

It isn't a problem to leave these role assignments, but you can remove them using steps that are similar to other role assignments. For information about how to remove role assignments, see [Azure portal](role-assignments-portal.md#remove-role-assignments), [Azure PowerShell](role-assignments-powershell.md#remove-access), or [Azure CLI](role-assignments-cli.md#remove-access)

In PowerShell, if you try to remove the role assignments using the object ID and role definition name, and more than one role assignment matches your parameters, you will get the error message: "The provided information does not map to a role assignment". The following shows an example of the error message:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

If you get this error message, make sure you also specify the `-Scope` or `-ResourceGroupName` parameters.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>Não estão sendo detectadas alterações de RBAC

Às vezes, o Azure Resource Manager armazena em cache configurações e dados para melhorar o desempenho. Ao criar ou excluir atribuições de função, poderá demorar até 30 minutos para que as alterações tenham efeito. Se estiver usando o portal do Azure, o Azure PowerShell ou a CLI do Azure, será possível forçar uma atualização das alterações de atribuição de função, saindo e entrando novamente. Se estiver fazendo alterações de atribuição de função com chamadas à API REST, poderá forçar uma atualização atualizando o token de acesso.

## <a name="web-app-features-that-require-write-access"></a>Recursos de aplicativo Web que exigem acesso para gravação

Se você conceder a um usuário o acesso somente leitura a um aplicativo Web, para sua surpresa, alguns recursos estarão desabilitados. As funcionalidades de gerenciamento a seguir exigem o acesso de **gravação** em um aplicativo Web (Colaborador ou Proprietário) e não estão disponíveis em um cenário somente leitura.

* Comandos (como iniciar, parar, etc.)
* Alterar configurações como configuração geral, configurações de escala, configurações de backup e configurações de monitoramento.
* Acessar credenciais de publicação e outros segredos como configurações de aplicativos e cadeias de conexão.
* Logs de streaming
* Configuração dos logs de diagnóstico
* Console (prompt de comando)
* Ativo e implantações recentes (para a implantação contínua do git local)
* Gasto estimado
* Testes da Web
* Rede virtual (somente visível para um leitor se uma rede virtual foi anteriormente configurada por um usuário com acesso para gravação).

Se você não conseguir acessar nenhum desses blocos, precisará solicitar ao administrador o acesso de Colaborador ao aplicativo Web.

## <a name="web-app-resources-that-require-write-access"></a>Recursos de aplicativos Web que exigem acesso para gravação

Os aplicativos Web são complicados pela presença de alguns recursos diferentes que interagem. Aqui está um grupo de recursos típico com alguns sites:

![Grupo de recursos do aplicativo Web](./media/troubleshooting/website-resource-model.png)

Como resultado, se você conceder a alguém o acesso somente ao aplicativo Web, muitas das funcionalidades na folha do site no portal do Azure estarão desabilitadas.

Estes itens exigem acesso para **gravação** no **Plano do Serviço de Aplicativo** que corresponde ao seu site:  

* Exibindo o tipo de preço do aplicativo Web (Gratuito ou Standard)  
* Configuração de escala (número instâncias, tamanho da máquina virtual, configurações de escalonamento automático)  
* Cotas (armazenamento, largura de banda, CPU)  

Estes itens exigem acesso para **gravação** no **Grupo de recursos** inteiro que contém o seu site:  

* Associações e Certificados SSL (os certificados SSL podem ser compartilhados entre sites no mesmo grupo de recursos e localização geográfica)  
* Regras de alerta  
* Configurações de autoescala  
* Componentes do Application insights  
* Testes da Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Recursos da máquina virtual que exigem acesso para gravação

Semelhante aos aplicativos Web, alguns recursos na folha da máquina virtual exigem acesso para gravação à máquina virtual ou a outros recursos no grupo de recursos.

As máquinas virtuais são relacionadas a nomes de domínio, redes virtuais, contas de armazenamento e regras de alerta.

Estes itens exigem acesso para **gravação** na **Máquina virtual**:

* Pontos de Extremidade  
* Endereços IP  
* Discos  
* Extensões  

Estes exigem acesso para **gravação** tanto na **Máquina virtual** quanto no **Grupo de recursos** (juntamente com o Nome de domínio) encontrados em:  

* Conjunto de disponibilidade  
* Conjunto de balanceamento de carga  
* Regras de alerta  

Se você não conseguir acessar nenhum desses blocos, solicite ao administrador o acesso de Colaborador ao Grupo de recursos.

## <a name="azure-functions-and-write-access"></a>Azure Functions e acesso para gravação

Alguns recursos do [Azure Functions](../azure-functions/functions-overview.md) exigem acesso de gravação. For example, if a user is assigned the [Reader](built-in-roles.md#reader) role, they will not be able to view the functions within a function app. O portal exibirá **(Sem acesso)** .

![Aplicativos de funções sem acesso](./media/troubleshooting/functionapps-noaccess.png)

Um leitor pode clicar na guia **Recursos da plataforma** e, em seguida, clicar em **Todas as configurações** para exibir algumas configurações relacionadas a um aplicativo de funções (semelhante a um aplicativo Web), mas não pode modificar essas configurações. To access these features, you will need the [Contributor](built-in-roles.md#contributor) role.

## <a name="next-steps"></a>Próximos passos

- [Troubleshoot for guest users](role-assignments-external-users.md#troubleshoot)
- [Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](role-assignments-portal.md)
- [Exibir logs de atividades para alterações de RBAC para recursos do Azure](change-history-report.md)

