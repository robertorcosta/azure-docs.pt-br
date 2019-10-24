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
ms.date: 08/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 158222c256e3efc7ca87d7a3781ca68e1c4307b1
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750183"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Solução de problemas com o RBAC para recursos do Azure

Este artigo responde a perguntas comuns sobre o RBAC (controle de acesso baseado em função) para recursos do Azure, para que você saiba o que esperar ao usar as funções no portal do Azure e possa solucionar problemas de acesso.

## <a name="problems-with-rbac-role-assignments"></a>Problemas com as atribuições de função RBAC

- Se não for possível adicionar uma atribuição de função no portal do Azure no **controle de acesso (iam)** porque a opção **Adicionar**  > **Adicionar atribuição de função** está desabilitada ou porque você recebe o erro de permissões "o cliente com a ID de objeto não tem autorização para executar a ação ", verifique se você está conectado atualmente a um usuário que recebe uma função que tem a permissão `Microsoft.Authorization/roleAssignments/write`, como [proprietário](built-in-roles.md#owner) ou [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) no escopo ao qual você está tentando atribuir a função.
- Se você receber a mensagem de erro "não é possível criar mais atribuições de função (código: RoleAssignmentLimitExceeded)" ao tentar atribuir uma função, tente reduzir o número de atribuições de função atribuindo funções a grupos. O Azure dá suporte a até **2.000** atribuições de função por assinatura.

## <a name="problems-with-custom-roles"></a>Problemas com funções personalizadas

- Se você precisar de etapas para criar uma função personalizada, consulte os tutoriais de função personalizada usando [Azure PowerShell](tutorial-custom-role-powershell.md) ou [CLI do Azure](tutorial-custom-role-cli.md).
- Se não for possível atualizar uma função personalizada existente, verifique se você está conectado atualmente a um usuário que é atribuído a uma função que tem a permissão `Microsoft.Authorization/roleDefinition/write`, como [proprietário](built-in-roles.md#owner) ou [administrador de acesso do usuário](built-in-roles.md#user-access-administrator).
- Se não for possível excluir uma função personalizada e receber a mensagem de erro "há atribuições de função existentes que fazem referência à função (código: RoleDefinitionHasAssignments)", há atribuições de função que ainda usam a função personalizada. Remova essas atribuições de função e tente excluir a função personalizada novamente.
- Se você receber a mensagem de erro "Limite de definição de função excedido. Não é possível criar mais definições de função (código: RoleDefinitionLimitExceeded) "quando você tenta criar uma nova função personalizada, exclua todas as funções personalizadas que não estão sendo usadas. O Azure dá suporte a até **5000** funções personalizadas em um locatário. (Para nuvens especializados, como o Azure Governamental, Azure Alemanha e Azure China 21Vianet, o limite é de 2000 funções personalizadas.)
- Se você receber um erro semelhante a "o cliente tem permissão para executar a ação ' Microsoft. Authorization/roleDefinitions/Write ' no escopo '/subscriptions/{SubscriptionId} ', no entanto, a assinatura vinculada não foi encontrada" quando você tentar atualizar uma função personalizada, verifique se um ou mais [escopos atribuíveis](role-definitions.md#assignablescopes) foram excluídos no locatário. Se o escopo tiver sido excluído, crie um tíquete de suporte, pois não há nenhuma solução de autoatendimento disponível no momento.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Recuperar RBAC quando as assinaturas são movidas entre locatários

- Se você precisar de etapas sobre como transferir uma assinatura para um locatário diferente do Azure AD, consulte [transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md).
- Quando você transfere uma assinatura a outro locatário do AAD, todas as atribuições de função são excluídas permanentemente do locatário de origem e não são migradas para o locatário de destino. Você precisa recriar as atribuições de função no locatário de destino. Você também precisa recriar manualmente as identidades gerenciadas dos recursos do Azure. Para obter mais informações, consulte [perguntas frequentes e problemas conhecidos com identidades gerenciadas](../active-directory/managed-identities-azure-resources/known-issues.md).
- Se você for um administrador global do Azure AD e não tiver acesso a uma assinatura após sua movimentação entre locatários, use a opção **Gerenciamento de acesso para recursos do Azure** para [elevar temporariamente seu acesso](elevate-access-global-admin.md) para obter acesso à assinatura.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemas com os administradores de serviço ou coadministradores

- Se você estiver tendo problemas com o administrador de serviços ou coadministradores, consulte [Adicionar ou alterar os administradores de assinatura do Azure](../billing/billing-add-change-azure-subscription-administrator.md) e as funções de [administrador de assinatura clássica, funções de RBAC do Azure e funções de administrador do Azure ad](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Acesso negado ou erros de permissão

- Se você receber o erro de permissões "o cliente com a ID de objeto não tem autorização para executar a ação sobre o escopo (código: AuthorizationFailed)" ao tentar criar um recurso, verifique se você está conectado no momento a um usuário que recebe uma função que tem gravação permissão para o recurso no escopo selecionado. Por exemplo, para gerenciar máquinas virtuais em um grupo de recursos, você deverá ter a função [Colaborador da Máquina Virtual](built-in-roles.md#virtual-machine-contributor) no grupo de recursos (ou escopo pai). Para obter uma lista das permissões de cada função interna, confira [Funções internas para recursos do Azure](built-in-roles.md).
- Se você receber o erro de permissões "você não tem permissão para criar uma solicitação de suporte" ao tentar criar ou atualizar um tíquete de suporte, verifique se você está conectado atualmente a um usuário que recebe uma função que tem a permissão `Microsoft.Support/supportTickets/write` , como [colaborador de solicitação de suporte](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Atribuições de função com entidade de segurança desconhecida

Ao listar suas atribuições de função usando Azure PowerShell, você poderá ver atribuições com um `DisplayName` vazio e um `ObjectType` definido como desconhecido. Por exemplo, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) retorna uma atribuição de função semelhante à seguinte:

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

Da mesma forma, ao listar suas atribuições de função usando CLI do Azure, você poderá ver atribuições com um `principalName` vazio. Por exemplo, a [lista de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-list) retorna uma atribuição de função semelhante à seguinte:

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

Essas atribuições de função ocorrem quando você atribui uma função a uma entidade de segurança (usuário, grupo, entidade de serviço ou identidade gerenciada) e depois exclui essa entidade de segurança. Essas atribuições de função não são exibidas no portal do Azure e não é um problema para deixá-las. No entanto, se desejar, você pode remover essas atribuições de funções.

Para remover essas atribuições de função, use os comandos de [exclusão de atribuição de função](/cli/azure/role/assignment#az-role-assignment-delete) [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) ou AZ.

No PowerShell, se você tentar remover as atribuições de função usando a ID de objeto e o nome de definição de função, e mais de uma atribuição de função corresponder aos parâmetros, você receberá a mensagem de erro: "as informações fornecidas não são mapeadas para uma atribuição de função". Veja a seguir um exemplo da mensagem de erro:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Se você receber essa mensagem de erro, certifique-se de especificar também os parâmetros `-Scope` ou `-ResourceGroupName`.

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

Alguns recursos do [Azure Functions](../azure-functions/functions-overview.md) exigem acesso de gravação. Por exemplo, se uma função de [leitor](built-in-roles.md#reader) for atribuída a um usuário, ela não poderá exibir as funções em um aplicativo de funções. O portal exibirá **(Sem acesso)** .

![Aplicativos de funções sem acesso](./media/troubleshooting/functionapps-noaccess.png)

Um leitor pode clicar na guia **Recursos da plataforma** e, em seguida, clicar em **Todas as configurações** para exibir algumas configurações relacionadas a um aplicativo de funções (semelhante a um aplicativo Web), mas não pode modificar essas configurações. Para acessar esses recursos, você precisará da função de [colaborador](built-in-roles.md#contributor) .

## <a name="next-steps"></a>Próximos passos

- [Solucionar problemas de usuários convidados](role-assignments-external-users.md#troubleshoot)
- [Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](role-assignments-portal.md)
- [Exibir logs de atividades para alterações de RBAC para recursos do Azure](change-history-report.md)

