---
title: Automatize a adição de um usuário de laboratório no Azure DevTest Labs | Microsoft Docs
description: Este artigo mostra como automatizar a adição de um usuário a um laboratório no Azure DevTest Labs usando modelos do Azure Resource Manager, PowerShell e CLI.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 70a6359923734c83590d4677bb2c93966c925d14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718132"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatize adicionar um usuário de laboratório a um laboratório no Azure DevTest Labs
O Azure DevTest Labs permite criar rapidamente ambientes de teste de dev de autoatendimento usando o portal Azure. No entanto, se você tiver várias equipes e várias instâncias do DevTest Labs, automatizar o processo de criação pode economizar tempo. [Os modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) permitem criar laboratórios, VMs de laboratório, imagens personalizadas, fórmulas e adicionar usuários de forma automatizada. Este artigo se concentra especificamente em adicionar usuários a uma instância do DevTest Labs.

Para adicionar um usuário a um laboratório, você adiciona o usuário à função **de usuário do DevTest Labs** para o laboratório. Este artigo mostra como automatizar a adição de um usuário a um laboratório usando uma das seguintes maneiras:

- Modelos do Azure Resource Manager
- Cmdlets do Azure PowerShell 
- CLI do Azure.

## <a name="use-azure-resource-manager-templates"></a>Usar modelos do Gerenciador de Recursos do Azure
O modelo seguinte do Gerenciador de recursos de amostra especifica um usuário a ser adicionado à função de usuário do **DevTest Labs** de um laboratório. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Se você estiver atribuindo a função no mesmo modelo que está criando o laboratório, lembre-se de adicionar uma dependência entre o recurso de atribuição de funções e o laboratório. Para obter mais informações, consulte [Definindo dependências no artigo Azure Resource Manager Templates.](../azure-resource-manager/templates/define-resource-dependency.md)

### <a name="role-assignment-resource-information"></a>Informações sobre recursos de atribuição de função
O recurso de atribuição de função precisa especificar o tipo e o nome.

A primeira coisa a notar é que o `Microsoft.Authorization/roleAssignments` tipo de recurso não é como seria para um grupo de recursos.  Em vez disso, o `{provider-namespace}/{resource-type}/providers/roleAssignments`tipo de recurso segue o padrão . Neste caso, o tipo `Microsoft.DevTestLab/labs/providers/roleAssignments`de recurso será .

O próprio nome da atribuição de papéis precisa ser globalmente único.  O nome da atribuição `{labName}/Microsoft.Authorization/{newGuid}`usa o padrão. O `newGuid` é um valor de parâmetro para o modelo. Ele garante que o nome da atribuição da função seja único. Como não há funções de modelo para criar GUIDs, você precisa gerar um GUID você mesmo usando qualquer ferramenta de gerador GUID.  

No modelo, o nome para a atribuição da função é definido pela `fullDevTestLabUserRoleName` variável. A linha exata do modelo é:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Propriedades de recurso de atribuição de função
Uma atribuição de função em si define três propriedades. Precisa `roleDefinitionId`do, `principalId`e `scope`.

### <a name="role-definition"></a>Definição de função
A definição de função ID é o identificador de string para a definição de função existente. O ID do papel `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`está na forma . 

O ID de assinatura `subscription().subscriptionId` é obtido usando a função modelo.  

Você precisa obter a definição `DevTest Labs User` de papel para o papel embutido. Para obter o GUID para a função [de usuário do DevTest Labs,](../role-based-access-control/built-in-roles.md#devtest-labs-user) você pode usar a [API Rest Deatribuições de função](/rest/api/authorization/roleassignments) ou o cmdlet [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0)

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

O ID da função é definido `devTestLabUserRoleId`na seção variáveis e nomeado . No modelo, o ID da função é definido como: 1111111111-0000-0000-111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID da entidade de segurança
O ID principal é o ID do objeto do usuário, grupo ou princípio do serviço do Active Directory que você deseja adicionar como usuário de laboratório ao laboratório. O modelo `ObjectId` usa o parâmetro como parâmetro.

Você pode obter o ObjectId usando os cmdlets [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup, ou [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell cmdlets. Esses cmdlets retornam um único ou listas de objetos do Active Directory que possuem uma propriedade ID, que é o ID do objeto que você precisa. O exemplo a seguir mostra como obter o ID do objeto de um único usuário em uma empresa.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Você também pode usar os cmdlets PowerShell do Diretório Ativo do Azure que incluem [Get-MsolUser,](/powershell/module/msonline/get-msoluser?view=azureadps-1.0) [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)e [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Escopo
O escopo especifica o grupo de recursos ou recursos para o qual a atribuição de função deve ser aplicada. Para recursos, o escopo está `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`na forma: . O modelo `subscription().subscriptionId` usa a função `subscription-id` para `resourceGroup().name` preencher a peça `resource-group-name` e a função de modelo para preencher a peça. O uso dessas funções significa que o laboratório para o qual você está atribuindo uma função deve existir na assinatura atual e no mesmo grupo de recursos ao qual a implantação do modelo é feita. A última `resource-name`parte é o nome do laboratório. Este valor é recebido através do parâmetro modelo neste exemplo. 

O escopo de função no modelo: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Implantação do modelo
Primeiro, crie um arquivo de parâmetros (por exemplo: azuredeploy.parameters.json) que passa valores para parâmetros no modelo Gerenciador de recursos. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Em seguida, use o cmdlet PowerShell do [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) para implantar o modelo Gerenciador de recursos. O comando de exemplo a seguir atribui uma pessoa, grupo ou um diretor de serviço à função de usuário do DevTest Labs para um laboratório.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

É importante notar que o nome de implantação do grupo e a atribuição de função GUID precisam ser únicos. Se você tentar implantar uma atribuição de recursos com um `RoleAssignmentUpdateNotPermitted` GUID não único, então você terá um erro.

Se você planeja usar o modelo várias vezes para adicionar vários objetos do Active Directory à função de usuário do DevTest Labs para o seu laboratório, considere usar objetos dinâmicos no comando PowerShell. O exemplo a seguir usa o cmdlet [Do Novo-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) para especificar o nome de implantação do grupo de recursos e a atribuição de função GUID dinamicamente.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
Como discutido na introdução, você cria uma nova atribuição de função Azure para adicionar um usuário à função de usuário do **DevTest Labs** para o laboratório. No PowerShell, você faz isso usando o [cmdlet New-AzureRMRoleAssignment.](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) Este cmdlet tem muitos parâmetros opcionais para permitir flexibilidade. As `ObjectId` `SigninName`, `ServicePrincipalName` ou podem ser especificadas como o objeto que está sendo concedido permissões.  

Aqui está uma amostra do comando Azure PowerShell que adiciona um usuário à função de usuário do DevTest Labs no laboratório especificado.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Para especificar o recurso ao qual as permissões estão `ResourceName` `ResourceType`sendo `ResourceGroup` concedidas `scope` pode ser especificado por uma combinação de , ou pelo parâmetro. Qualquer combinação de parâmetros é usada, forneça informações suficientes ao cmdlet para identificar exclusivamente o objeto Active Directory (usuário, grupo ou principal de serviço), escopo (grupo de recursos ou recurso) e definição de função.

## <a name="use-azure-command-line-interface-cli"></a>Use a Cli (Interface de Linha de Comando do Azure)
No Azure CLI, adicionar um usuário de laboratório `az role assignment create` a um laboratório é feito usando o comando. Para obter mais informações sobre os cmdlets azure CLI, consulte [Gerenciar o acesso aos recursos do Azure usando o RBAC e o Azure CLI](../role-based-access-control/role-assignments-cli.md).

O objeto que está sendo concedido acesso `objectId` `signInName`pode `spn` ser especificado pelos parâmetros, e. O laboratório ao qual o objeto está sendo `scope` acesso pode ser `resource-name` `resource-type`identificado `resource-group` pela url ou uma combinação dos parâmetros e parâmetros.

O exemplo a seguir do Azure CLI mostra como adicionar uma pessoa à função de usuário do DevTest Labs para o Laboratório especificado.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Criar e gerenciar máquinas virtuais com DevTest Labs usando a CLI do Azure](devtest-lab-vmcli.md)
- [Crie uma máquina virtual com devTest Labs usando o Azure PowerShell](devtest-lab-vm-powershell.md)
- [Use ferramentas de linha de comando para iniciar e parar as máquinas virtuais Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

