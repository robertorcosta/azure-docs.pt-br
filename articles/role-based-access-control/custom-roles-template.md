---
title: Criar uma função personalizada do Azure usando um modelo de Azure Resource Manager – RBAC do Azure
description: Saiba como criar uma função personalizada do Azure usando modelos de Azure Resource Manager e o Azure RBAC (controle de acesso baseado em função).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85397908"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>Criar uma função personalizada do Azure usando um modelo de Azure Resource Manager

Se as [funções internas do Azure](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias [funções personalizadas](custom-roles.md). Este artigo descreve como criar uma função personalizada usando um modelo de Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma função personalizada, você deve ter:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de acesso do usuário](built-in-roles.md#user-access-administrator)

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, especifique um nome de função, permissões e onde a função pode ser usada. Neste artigo, você cria uma função chamada "Custom role-RG Reader" com permissões de recurso que podem ser atribuídas em um escopo de assinatura ou inferior.

### <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste artigo é dos [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def). O modelo tem quatro parâmetros e uma seção de recursos. Os quatro parâmetros são:

- Matriz de ações com um valor padrão de ["Microsoft. Resources/subscriptions/resourceGroups/Read"]
- Matriz de ações com um valor padrão vazio
- Nome da função com um valor padrão de "função personalizada-leitor RG"
- Descrição da função com um valor padrão de "implantação de nível de assinatura de uma definição de função"

O recurso definido no modelo inclui:

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

O escopo onde essa função personalizada pode ser atribuída é definido como a assinatura atual.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>Implantar o modelo

Siga estas etapas para implantar o modelo anterior.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Abra o Azure Cloud Shell para PowerShell.

1. Copie e cole o script a seguir no Cloud Shell.

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Insira um local para a implantação, como *centralus*.

1. Insira uma lista de ações para a função personalizada como uma lista separada por vírgulas, como Microsoft. Resources */Resources/Read, Microsoft. Resources/subscriptions/resourceGroups/Read*.

1. Se necessário, pressione ENTER para executar o comando New-AzDeployment.

    O comando [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) implanta o modelo para criar a função personalizada.

    Será exibida uma saída semelhante à seguinte:

    ```azurepowershell
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    
    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Siga estas etapas para verificar se a função personalizada foi criada.

1. Execute o comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) para listar a função personalizada.

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Você deverá ver uma saída semelhante à seguinte:

    ```azurepowershell
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. Na portal do Azure, abra sua assinatura.

1. No menu à esquerda, clique em **Controle de acesso (IAM)** .

1. Clique na guia **funções** .

1. Defina a lista de **tipos** como **CustomRole**.

1. Verifique se a função **personalizada do leitor de RG** está listada.

   ![Nova função personalizada no portal do Azure](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a função personalizada, siga estas etapas.

1. Execute o comando a seguir para remover a função personalizada.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Digite **Y** para confirmar que você deseja remover a função personalizada.

## <a name="next-steps"></a>Próximas etapas

- [Entender as definições de função do Azure](role-definitions.md)
- [Início Rápido: Adicione uma atribuição de função do Azure usando um modelo do Azure Resource Manager](quickstart-role-assignments-template.md)
- [Documentação do modelo ARM](../azure-resource-manager/templates/index.yml)
