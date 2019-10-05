---
title: Implantar recursos com o PowerShell e o modelo | Microsoft Docs
description: Use Azure Resource Manager e Azure PowerShell para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: tomfitz
ms.openlocfilehash: 88aabb676d3a15dd2efff3acd751818301519ae1
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972700"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implantar recursos com modelos do Resource Manager e o Azure PowerShell

Saiba como usar o Azure PowerShell com modelos do Resource Manager para implantar seus recursos no Azure. Para saber mais sobre os conceitos de implantação e gerenciamento das soluções do Azure, confira [Visão geral do Azure Resource Manager](resource-group-overview.md).

## <a name="deployment-scope"></a>Escopo da implantação

Você pode direcionar sua implantação para uma assinatura do Azure ou um grupo de recursos em uma assinatura. Na maioria dos casos, você direcionará a implantação para um grupo de recursos. Use implantações de assinatura para aplicar políticas e atribuições de função na assinatura. Você também usa implantações de assinatura para criar um grupo de recursos e implantar recursos nele. Dependendo do escopo da implantação, você usará comandos diferentes.

Para implantar em um **grupo de recursos**, use [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Para implantar em uma **assinatura**, use [New-AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Atualmente, as implantações de grupo de gerenciamento só têm suporte por meio da API REST. Consulte [implantar recursos com modelos do Resource Manager e a API REST do Gerenciador de recursos](resource-group-template-deploy-rest.md).

Os exemplos neste artigo usam implantações de grupo de recursos. Para obter mais informações sobre implantações de assinatura, consulte [criar grupos de recursos e recursos no nível da assinatura](deploy-to-subscription.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de um modelo para implantar. Se você ainda não tiver um, baixe e salve um [modelo de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) do repositório de modelos de início rápido do Azure. O nome do arquivo local usado neste artigo é **c:\MyTemplates\azuredeploy.json**.

A menos que você use o Azure cloud Shell para implantar modelos, você precisa instalar Azure PowerShell e conectar-se ao Azure:

- **Instalar cmdlets do Azure PowerShell em seu computador local.** Para obter mais informações, consulte [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).
- **Conectar-se ao Azure usando [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** . Se você tiver várias assinaturas do Azure, talvez precise executar também [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Para saber mais, confira [Use multiple Azure subscriptions](/powershell/azure/manage-subscriptions-azureps) (Usar várias assinaturas do Azure).

## <a name="deploy-local-template"></a>Implantar o modelo local

O exemplo a seguir cria um grupo de recursos e implanta um modelo de seu computador local. O nome do grupo de recursos pode incluir somente caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses. Pode ter até 90 caracteres. Ele não pode terminar em um período.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

A implantação pode levar alguns minutos para ser concluída.

## <a name="deploy-remote-template"></a>Implantar modelo remoto

Em vez de armazenar modelos do Resource Manager no computador local, talvez você prefira armazená-los em um local externo. É possível armazenar modelos em um repositório de controle de código-fonte (como o GitHub). Ou ainda armazená-los em uma conta de armazenamento do Azure para acesso compartilhado na sua organização.

Para implantar um modelo externo, use o parâmetro **TemplateUri**. Use o URI do exemplo para implantar o modelo de exemplo do GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

O exemplo anterior requer um URI acessível publicamente para o modelo, que funciona para a maioria dos cenários porque seu modelo não deve incluir dados confidenciais. Se você precisar especificar dados confidenciais (como uma senha de administrador), passe esse valor como um parâmetro seguro. No entanto, se você não quiser que seu modelo seja acessível publicamente, poderá protegê-lo armazenando-o em um contêiner de armazenamento particular. Para obter informações sobre como implantar um modelo que exige um token SAS (assinatura de acesso compartilhado), confira [Implantar modelo particular com o token SAS](resource-manager-powershell-sas-token.md). Para percorrer um tutorial, veja [Tutorial: Integrar o Azure Key Vault na implantação de Modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Implantar do Azure cloud Shell

É possível usar o [Azure Cloud Shell](https://shell.azure.com) para implantar o modelo. Para implantar um modelo externo, forneça o URI do modelo. Para implantar um modelo local, você deve carregar o modelo primeiro para a conta de armazenamento do seu Cloud Shell. Para carregar arquivos para o shell, selecione o ícone de menu **Carregar/Baixar arquivos** na janela do shell.

Para abrir o Cloud Shell, navegue até [https://shell.azure.com](https://shell.azure.com) ou selecione **Try-It** na seção de código a seguir:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Para colar o código no shell, clique com o botão direito do mouse dentro do shell e, em seguida, selecione **Colar**.

## <a name="pass-parameter-values"></a>Passar valores de parâmetro

Para passar valores de parâmetros, você pode usar parâmetros inline ou um arquivo de parâmetros.

### <a name="inline-parameters"></a>Parâmetros embutidos

Para passar parâmetros inline, forneça os nomes do parâmetro com o comando `New-AzResourceGroupDeployment`. Por exemplo, para passar uma string e array para um template, use:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Você também pode obter o conteúdo do arquivo e fornecer esse conteúdo como um parâmetro embutido.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Obtendo um valor de parâmetro de um arquivo é útil quando você precisa fornecer valores de configuração. Por exemplo, você pode fornecer [valores de cloud-init para uma máquina virtual Linux](../virtual-machines/linux/using-cloud-init.md).

Se você precisar passar uma matriz de objetos, crie tabelas de hash no PowerShell e adicione-as a uma matriz. Passe essa matriz como um parâmetro durante a implantação.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Arquivos de parâmetros

Em vez de passar parâmetros como valores embutidos no script, talvez seja mais fácil usar um arquivo JSON que contenha os valores de parâmetro. O arquivo de parâmetro pode ser um arquivo local ou um arquivo externo com um URI acessível.

Para obter mais informações sobre o arquivo de parâmetro, consulte [criar arquivo de parâmetro do Resource Manager](resource-manager-parameter-files.md).

Para passar um arquivo de parâmetro local, use o **TemplateParameterFile**:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para passar um arquivo de parâmetro externo, use o **TemplateParameterUri**:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Testar implantações do modelo

Para testar seus valores de parâmetro e modelo sem realmente implantar os recursos, use [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Se nenhum erro for detectado, o comando será concluído sem uma resposta. Se um erro for detectado, o comando retornará uma mensagem de erro. Por exemplo, passando um valor incorreto para a SKU da conta de armazenamento, retorna o seguinte erro:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Se seu modelo tem um erro de sintaxe, o comando retorna um erro indicando que ele não foi possível analisar o modelo. A mensagem indica o número da linha e a posição do erro de análise.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Próximas etapas

- Para reverter para uma implantação bem-sucedida quando você receber um erro, consulte [reverter em caso de erro para a implantação bem-sucedida](rollback-on-error.md).
- Para especificar como lidar com os recursos existentes no grupo de recursos, mas que não estão definidos no modelo, confira [Modos de implantação do Azure Resource Manager](deployment-modes.md).
- Para entender como definir parâmetros em seu modelo, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](resource-group-authoring-templates.md).
- Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](resource-manager-powershell-sas-token.md).
