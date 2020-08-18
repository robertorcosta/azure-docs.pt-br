---
title: Criar e implantar especificação de modelo
description: Saiba como criar uma especificação usando um modelo do ARM. Implante a especificação de modelo em um grupo de recursos em sua assinatura.
author: tfitzmac
ms.date: 08/06/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 8fe9ec46050ad831430239b960a7f528af7f4dc2
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924318"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Início Rápido: Criar e implantar especificação de modelo (versão prévia)

Este início rápido mostra como empacotar um modelo do ARM (Azure Resource Manager) em uma [especificação de modelo](template-specs.md) e implantar essa especificação. A especificação de modelo contém um modelo do ARM que implanta uma conta de armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> As especificações de modelo estão atualmente em versão prévia. Para usá-las, você deve [inscrever-se na lista de espera](https://aka.ms/templateSpecOnboarding).
>
> Depois de ser aprovado na lista de espera, você obterá instruções para a instalação do módulo do PowerShell em versão prévia.

## <a name="create-template-spec"></a>Criar especificação de modelo

A especificação de modelo é um novo tipo de recurso chamado **Microsoft.Resources/templateSpecs**. Para criar sua especificação de modelo, você pode usar o Azure PowerShell ou um modelo do ARM. Em todas as opções, você precisa de um modelo do ARM que é empacotado dentro da especificação de modelo.

Com o PowerShell, o modelo do ARM é passado como um parâmetro para o comando. Com o modelo do ARM, o modelo a ser empacotado na especificação é inserido na definição dela.

Essas opções são mostradas abaixo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Ao criar uma especificação de modelo com o PowerShell, você poderá passar um modelo local. Copie o modelo a seguir e salve-o localmente em um arquivo chamado **azuredeploy.json**. Este início rápido assume que você salvou no caminho **c:\Templates\azuredeploy.json**, mas é possível usar qualquer caminho.

   :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Crie um grupo de recursos para conter a especificação de modelo.

   ```azurepowershell
   New-AzResourceGroup `
     -Name templateSpecRG `
     -Location westus2
   ```

1. Crie a especificação de modelo nesse grupo de recursos. Nomeie a nova especificação de modelo como **storageSpec**.

   ```powershell
   New-AzTemplateSpec `
     -ResourceGroupName templateSpecRG `
     -Name storageSpec `
     -Version "1.0" `
     -Location westus2 `
     -TemplateJsonFile "c:\Templates\azuredeploy.json"
   ```

# <a name="arm-template"></a>[Modelo do ARM](#tab/azure-resource-manager)

1. Quando você usa um modelo do ARM para criar a especificação de modelo, o modelo é incorporado na definição de recursos. Copie o modelo a seguir e salve-o localmente como **azuredeploy.json**. Este início rápido assume que você salvou no caminho **c:\Templates\azuredeploy.json**, mas é possível usar qualquer caminho.

   > [!NOTE]
   > No modelo incorporado, todos os colchetes esquerdos devem ser precedidos por um segundo colchete esquerdo. Use `[[` em vez de `[`.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "functions": [],
       "variables": {},
       "resources": [
           {
               "type": "Microsoft.Resources/templateSpecs",
               "apiVersion": "2019-06-01-preview",
               "name": "storageSpec",
               "location": "westus2",
               "properties": {
                   "displayName": "Storage template spec"
               },
               "tags": {},
               "resources": [
                   {
                       "type": "versions",
                       "apiVersion": "2019-06-01-preview",
                       "name": "1.0",
                       "location": "westus2",
                       "dependsOn": [ "storageSpec" ],
                       "properties": {
                           "template": {
                               "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                               "contentVersion": "1.0.0.0",
                               "parameters": {
                                   "storageAccountType": {
                                       "type": "string",
                                       "defaultValue": "Standard_LRS",
                                       "allowedValues": [
                                           "Standard_LRS",
                                           "Standard_GRS",
                                           "Standard_ZRS",
                                           "Premium_LRS"
                                       ],
                                       "metadata": {
                                           "description": "Storage Account type"
                                       }
                                   },
                                   "location": {
                                       "type": "string",
                                       "defaultValue": "[[resourceGroup().location]",
                                       "metadata": {
                                           "description": "Location for all resources."
                                       }
                                   }
                               },
                               "variables": {
                                   "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                               },
                               "resources": [
                                   {
                                       "type": "Microsoft.Storage/storageAccounts",
                                       "apiVersion": "2019-04-01",
                                       "name": "[[variables('storageAccountName')]",
                                       "location": "[[parameters('location')]",
                                       "sku": {
                                           "name": "[[parameters('storageAccountType')]"
                                       },
                                       "kind": "StorageV2",
                                       "properties": {}
                                   }
                               ],
                               "outputs": {
                                   "storageAccountName": {
                                       "type": "string",
                                       "value": "[[variables('storageAccountName')]"
                                   }
                               }
                           }
                       },
                       "tags": {}
                   }
               ]
           }
       ],
       "outputs": {}
   }
   ```

1. Use a CLI do Azure ou o PowerShell para criar um grupo de recursos.

   ```azurecli
   az group create \
     --name templateSpecRG \
     --location westus2
   ```

   ```azurepowershell
   New-AzResourceGroup `
     -Name templateSpecRG `
     -Location westus2
   ```

1. Implante seu modelo com a CLI do Azure ou o PowerShell.

   ```azurecli
   az deployment group create \
     --name templateSpecRG \
     --template-file "c:\Templates\azuredeploy.json"
   ```

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -ResourceGroupName templateSpecRG `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

---

## <a name="deploy-template-spec"></a>Implantar especificação de modelo

Agora você pode implantar a especificação de modelo. Implantar a especificação de modelo é igual à implantação do modelo que a contém, exceto pelo fato de que você passa a ID do recurso da especificação de modelo. Você usa os mesmos comandos de implantação e, se necessário, passa os valores de parâmetro para a especificação de modelo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie um grupo de recursos para conter a nova conta de armazenamento.

   ```azurepowershell
   New-AzResourceGroup `
     -Name storageRG `
     -Location westus2
   ```

1. Obtenha a ID do recurso da especificação de modelo.

   ```azurepowershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Version.Id
   ```

1. Implante a especificação de modelo.

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG
   ```

1. Você fornece parâmetros exatamente como faria para um modelo do ARM. Reimplante a especificação de modelo com um parâmetro para o tipo de conta de armazenamento.

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -StorageAccountType Standard_GRS
   ```

# <a name="arm-template"></a>[Modelo do ARM](#tab/azure-resource-manager)

1. Copie o modelo a seguir e salve-o localmente em um arquivo chamado **storage.json**.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "functions": [],
       "variables": {},
       "resources": [
           {
               "type": "Microsoft.Resources/deployments",
               "apiVersion": "2020-06-01",
               "name": "demo",
               "properties": {
                   "templateLink": {
                       "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
                   },
                   "parameters": {
                   },
                   "mode": "Incremental"
               }
           }
       ],
       "outputs": {}
   }
   ```

1. Use a CLI do Azure ou o PowerShell a fim de criar um grupo de recursos para a conta de armazenamento.

   ```azurecli
   az group create \
     --name storageRG \
     --location westus2
   ```

   ```azurepowershell
   New-AzResourceGroup `
     -Name storageRG `
     -Location westus2
   ```

1. Implante seu modelo com a CLI do Azure ou o PowerShell.

   ```azurecli
   az deployment group create \
     --name storageRG \
     --template-file "c:\Templates\storage.json"
   ```

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -ResourceGroupName storageRG `
     -TemplateFile "c:\Templates\storage.json"
   ```

---

## <a name="grant-access"></a>Conceder acesso

Se você quiser permitir que outros usuários na sua organização implantem sua especificação de modelo, precisará conceder acesso de leitura a eles. Você pode atribuir a função Leitor a um grupo do Azure AD do grupo de recursos que contém as especificações de modelo que deseja compartilhar. Para saber mais, confira [Tutorial: Conceder a um grupo acesso aos recursos do Azure usando o Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar o recurso implantado neste início rápido, exclua ambos os grupos de recursos criados.

1. No portal do Azure, selecione Grupo de recursos no menu à esquerda.

1. Insira o nome do grupo de recursos (templateSpecRG e storageRG) no campo Filtrar por nome.

1. Selecione o nome do grupo de recursos.

1. Selecione Excluir grupo de recursos no menu superior.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como criar uma especificação de modelo que inclua modelos vinculados, confira [Criar uma especificação de modelo com um modelo vinculado](template-specs-create-linked.md).
