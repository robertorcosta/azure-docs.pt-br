---
title: Conceder acesso para criar assinaturas do Azure Enterprise
description: Aprenda a conferir a um usuário ou entidade de serviço a capacidade de criar programaticamente assinaturas do Azure Enterprise.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478871"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)

Como cliente do Azure no [EA (Enterprise Agreement)](https://azure.microsoft.com/pricing/enterprise-agreement/), você pode conceder a outro usuário ou entidade de serviço permissão para criar assinaturas cobradas de sua conta. Neste artigo, você aprenderá a usar o [RBAC (Controle de Acesso Baseado em Função)](../../active-directory/role-based-access-control-configure.md) para compartilhar a capacidade de criar assinaturas, e aprenderá a fazer auditoria das criações de assinatura. É necessário ter a função Proprietário na conta que deseja compartilhar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Conceder acesso

Para [criar assinaturas em uma conta de inscrição,](programmatically-create-subscription.md)os usuários devem ter a função de Proprietário do [RBAC](../../role-based-access-control/built-in-roles.md#owner) nessa conta. Você pode conceder a um usuário ou a um grupo de usuários a função de Proprietário rbac em uma conta de inscrição seguindo estas etapas:

1. Obtenha o ID do objeto da conta de inscrição que você deseja conceder acesso

    Para conceder a outros o papel de Proprietário do RBAC em uma conta de inscrição, você deve ser o Proprietário da Conta ou um Proprietário rbac da conta.

    # <a name="rest"></a>[Resto](#tab/rest)

    Solicite listar todas as contas de matrícula a que você tenha acesso:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure responde com uma lista de todas as contas de registro a que você tem acesso:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    Use `principalName` o imóvel para identificar a conta à qual deseja conceder acesso ao Proprietário rbac. Copie `name` a conta dessa. Por exemplo, se você quisesse conceder acesso SignUpEngineering@contoso.com ao Proprietário do ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```RBAC à conta de inscrição, você copiaria . Este é o ID do objeto da conta de matrícula. Cole esse valor em algum lugar para que você `enrollmentAccountObjectId`possa usá-lo na próxima etapa como .

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell)

    Use o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de registro às quais você tem acesso. Selecione **Experimente** para abrir [o Azure Cloud Shell](https://shell.azure.com/). Para colar o código, clique com o botão direito do mouse nas janelas do shell e na **seleta Pasta**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    O Azure responde com uma lista de contas de inscrição a que você tem acesso:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Use `principalName` o imóvel para identificar a conta a que deseja conceder acesso ao Proprietário RBAC. Copie `ObjectId` a conta dessa. Por exemplo, se você quisesse conceder acesso SignUpEngineering@contoso.com ao Proprietário do ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```RBAC à conta de inscrição, você copiaria . Cole este id de objeto em algum lugar para que `enrollmentAccountObjectId`você possa usá-lo no próximo passo como o .

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    Use o comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) para listar todas as contas de registro às quais você tem acesso. Selecione **Experimente** para abrir [o Azure Cloud Shell](https://shell.azure.com/). Para colar o código, clique com o botão direito do mouse nas janelas do shell e na **seleta Pasta**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    O Azure responde com uma lista de contas de inscrição a que você tem acesso:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    Use `principalName` o imóvel para identificar a conta à qual deseja conceder acesso ao Proprietário rbac. Copie `name` a conta dessa. Por exemplo, se você quisesse conceder acesso SignUpEngineering@contoso.com ao Proprietário do ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```RBAC à conta de inscrição, você copiaria . Este é o ID do objeto da conta de matrícula. Cole esse valor em algum lugar para que você `enrollmentAccountObjectId`possa usá-lo na próxima etapa como .

1. <a id="userObjectId"></a>Obtenha iD de objeto do usuário ou grupo que você deseja dar a função de proprietário rbac para

    1. No portal Azure, pesquise no **Azure Active Directory**.
    1. Se você quiser conceder acesso a um usuário, clique em **Usuários** no menu à esquerda. Se você quiser conceder acesso a um grupo, clique em **Grupos**.
    1. Selecione o Usuário ou Grupo para o qual deseja dar a função de Proprietário rbac.
    1. Se você selecionou um Usuário, encontrará o ID do objeto na página Perfil. Se você selecionou um Grupo, o ID do objeto estará na página Visão Geral. Copie o **ObjectID** clicando no ícone à direita da caixa de texto. Cole isso em algum lugar para que você `userObjectId`possa usá-lo no próximo passo como .

1. Conceda ao usuário ou grupo a função de Proprietário do RBAC na conta de inscrição

    Usando os valores coletados nas duas primeiras etapas, conceda ao usuário ou grupo a função de Proprietário rbac na conta de inscrição.

    # <a name="rest"></a>[Resto](#tab/rest-2)

    Execute o comando a ```<enrollmentAccountObjectId>``` seguir, substituindo pelo `name` que```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```você copiou na primeira etapa (). Substitua pelo ```<userObjectId>``` ID do objeto copiado da segunda etapa.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Quando a função de Proprietário é atribuída com êxito no escopo da conta do registro, o Azure responde com informações sobre a atribuição de função:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell-2)

    Execute o seguinte comando [New-AzRoleAssignment,](../../active-directory/role-based-access-control-manage-access-powershell.md) substituindo-o ```<enrollmentAccountObjectId>``` `ObjectId` ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```coletado na primeira etapa (). Substitua pelo ```<userObjectId>``` ID do objeto coletado na segunda etapa.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    Execute o comando [seguinte a az role assignment create,](../../active-directory/role-based-access-control-manage-access-azure-cli.md) substituindo ```<enrollmentAccountObjectId>``` pelo `name` que você copiou na primeira etapa ().```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Substitua pelo ```<userObjectId>``` ID do objeto coletado na segunda etapa.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Uma vez que um usuário se torne um Proprietário RBAC para sua conta de inscrição, ele pode [criar assinaturas programáticamente](programmatically-create-subscription.md) ele. Uma assinatura criada por um usuário delegado ainda tem o Proprietário da Conta original como Admin de Serviço, mas também tem o usuário delegado como proprietário de RBAC por padrão.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Audite quem criou assinaturas usando logs de atividade

Para controlar as assinaturas criadas por meio desta API, use o [API de Locatário do Log de Atividade](/rest/api/monitor/tenantactivitylogs). No momento não é possível usar o PowerShell, CLI ou o portal do Azure para controlar a criação da assinatura.

1. Como um administrador de locatários do locatário do AD do Azure, [eleve o acesso](../../active-directory/role-based-access-control-tenant-admin-access.md), em seguida, atribua uma função de leitor para o usuário de auditoria sobre o escopo `/providers/microsoft.insights/eventtypes/management`.
1. Como o usuário de auditoria, chame o [API de Locatário do Log de Atividade](/rest/api/monitor/tenantactivitylogs) para ver as atividades de criação de assinatura. Exemplo:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Para chamar convenientemente esta API da linha de comando, tente [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Próximas etapas

* Agora que o usuário ou entidade de serviço tem permissão para criar uma assinatura, use essa identidade para [criar programaticamente as assinaturas do Azure Enterprise](programmatically-create-subscription.md).
* Para obter um exemplo sobre como criar assinaturas usando .NET, consulte [código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para saber mais sobre o Azure Resource Manager, confira [Visão geral do Azure Resource Manager](overview.md).
* Para saber mais sobre como gerenciar grandes números de assinaturas usando grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](../../governance/management-groups/overview.md)
* Para ver uma abrangente diretriz de práticas recomendadas para grandes organizações no controle de assinatura, consulte [scaffold enterprise do Azure - governança prescritivas de assinatura](/azure/architecture/cloud-adoption-guide/subscription-governance)
