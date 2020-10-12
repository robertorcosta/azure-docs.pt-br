---
title: Implantar recursos com o PowerShell e o modelo
description: Use Azure Resource Manager e Azure PowerShell para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: e47de54558962215fe3be78f5b9c45c8d46c54a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372435"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Implantar recursos com modelos do Resource Manager e o Azure PowerShell

Este artigo explica como usar Azure PowerShell com modelos de Azure Resource Manager (modelos ARM) para implantar seus recursos no Azure. Se você não estiver familiarizado com os conceitos de implantação e gerenciamento de suas soluções do Azure, consulte [visão geral da implantação de modelo](overview.md).

## <a name="deployment-scope"></a>Escopo da implantação

Você pode direcionar sua implantação para um grupo de recursos, uma assinatura, um grupo de gerenciamento ou um locatário. Na maioria dos casos, você direcionará a implantação para um grupo de recursos. Para aplicar políticas e atribuições de função em um escopo maior, use assinatura, grupo de gerenciamento ou implantações de locatário. Ao implantar em uma assinatura, você pode criar um grupo de recursos e implantar recursos nele.

Dependendo do escopo da implantação, você usará comandos diferentes.

* Para implantar em um **grupo de recursos**, use [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* Para implantar em uma **assinatura**, use New-AzSubscriptionDeployment:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Para saber mais sobre as implantações de nível de assinatura, confira [Criar grupos de recursos e recursos no nível da assinatura](deploy-to-subscription.md).

* Para implantar em um **grupo de gerenciamento**, use [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Para saber mais sobre implantações de nível de grupo de gerenciamento, confira [Criar recursos no nível de grupo de gerenciamento](deploy-to-management-group.md).

* Para implantar em um **locatário**, use [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Para saber mais sobre implantações de nível de locatário, confira [Criar recursos no nível de locatário](deploy-to-tenant.md).

Os exemplos neste artigo usam implantações de grupo de recursos.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de um modelo para implantar. Se você ainda não tiver um, baixe e salve um [modelo de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) do repositório de modelos de início rápido do Azure. O nome do arquivo local usado neste artigo é **c:\MyTemplates\azuredeploy.json**.

A menos que você use o Azure Cloud Shell para implantar modelos, você precisa instalar o Azure PowerShell e conectar-se ao Azure:

- **Instalar cmdlets do Azure PowerShell em seu computador local.** Para obter mais informações, consulte [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).
- **Conectar-se ao Azure usando [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Se você tiver várias assinaturas do Azure, talvez precise executar também [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Para saber mais, confira [Use multiple Azure subscriptions](/powershell/azure/manage-subscriptions-azureps) (Usar várias assinaturas do Azure).

## <a name="deploy-local-template"></a>Implantar o modelo local

O exemplo a seguir cria um grupo de recursos e implanta um modelo de seu computador local. O nome do grupo de recursos pode incluir somente caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses. Pode ter até 90 caracteres. Ele não pode terminar em um período.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -Name ExampleDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

A implantação pode levar alguns minutos para ser concluída.

## <a name="deployment-name"></a>Nome da implantação

No exemplo anterior, você nomeou a implantação `ExampleDeployment` . Se você não fornecer um nome para a implantação, o nome do arquivo de modelo será usado. Por exemplo, se você implantar um modelo chamado `azuredeploy.json` e não especificar um nome de implantação, a implantação será nomeada `azuredeploy` .

Toda vez que você executa uma implantação, uma entrada é adicionada ao histórico de implantação do grupo de recursos com o nome da implantação. Se você executar outra implantação e fornecer o mesmo nome, a entrada anterior será substituída pela implantação atual. Se você quiser manter entradas exclusivas no histórico de implantação, dê a cada implantação um nome exclusivo.

Para criar um nome exclusivo, você pode atribuir um número aleatório.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

Ou adicione um valor de data.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Se você executar implantações simultâneas no mesmo grupo de recursos com o mesmo nome de implantação, somente a última implantação será concluída. Todas as implantações com o mesmo nome que não foram concluídas são substituídas pela última implantação. Por exemplo, se você executar uma implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage1` e, ao mesmo tempo, executar outra implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage2` , você implanta apenas uma conta de armazenamento. A conta de armazenamento resultante é denominada `storage2` .

No entanto, se você executar uma implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage1` e imediatamente após a conclusão da execução de outra implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage2` , você tem duas contas de armazenamento. Um é chamado `storage1` , e o outro é nomeado `storage2` . Mas, você tem apenas uma entrada no histórico de implantação.

Ao especificar um nome exclusivo para cada implantação, você pode executá-los simultaneamente sem conflitos. Se você executar uma implantação chamada `newStorage1` que implanta uma conta de armazenamento denominada `storage1` e, ao mesmo tempo, executar outra implantação chamada `newStorage2` que implanta uma conta de armazenamento denominada `storage2` , você tem duas contas de armazenamento e duas entradas no histórico de implantação.

Para evitar conflitos com implantações simultâneas e para garantir entradas exclusivas no histórico de implantação, dê a cada implantação um nome exclusivo.

## <a name="deploy-remote-template"></a>Implantar modelo remoto

Em vez de armazenar modelos de ARM em seu computador local, você pode preferir armazená-los em um local externo. É possível armazenar modelos em um repositório de controle de código-fonte (como o GitHub). Ou ainda armazená-los em uma conta de armazenamento do Azure para acesso compartilhado na sua organização.

Para implantar um modelo externo, use o parâmetro **TemplateUri** . Use o URI do exemplo para implantar o modelo de exemplo do GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

O exemplo anterior requer um URI acessível publicamente para o modelo, que funciona para a maioria dos cenários porque seu modelo não deve incluir dados confidenciais. Se você precisar especificar dados confidenciais (como uma senha de administrador), passe esse valor como um parâmetro seguro. No entanto, se você não quiser que seu modelo seja acessível publicamente, poderá protegê-lo armazenando-o em um contêiner de armazenamento particular. Para obter informações sobre como implantar um modelo que exige um token SAS (assinatura de acesso compartilhado), confira [Implantar modelo particular com o token SAS](secure-template-with-sas-token.md). Para percorrer um tutorial, consulte [tutorial: integrar Azure Key Vault na implantação de modelo ARM](template-tutorial-use-key-vault.md).

## <a name="deploy-template-spec"></a>Implantar especificação de modelo

Em vez de implantar um modelo local ou remoto, você pode criar uma [especificação de modelo](template-specs.md). A especificação do modelo é um recurso em sua assinatura do Azure que contém um modelo do ARM. Ele facilita o compartilhamento seguro do modelo com usuários em sua organização. Use o controle de acesso baseado em função do Azure (RBAC do Azure) para conceder acesso à especificação do modelo. Este recurso está atualmente em visualização.

Os exemplos a seguir mostram como criar e implantar uma especificação de modelo. Esses comandos só estarão disponíveis se você tiver se [inscrito na versão prévia](https://aka.ms/templateSpecOnboarding).

Primeiro, você cria a especificação do modelo fornecendo o modelo ARM.

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateJsonFile ./mainTemplate.json
```

Em seguida, você obtém a ID da especificação do modelo e a implanta.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

Para obter mais informações, consulte [Azure Resource Manager de especificações de modelo (versão prévia)](template-specs.md).

## <a name="preview-changes"></a>Visualizar alterações

Antes de implantar seu modelo, você pode visualizar as alterações que o modelo fará no seu ambiente. Use a [operação What-If](template-deploy-what-if.md) para verificar se o modelo faz as alterações que você espera. O What-If também valida o modelo para erros.

## <a name="deploy-from-azure-cloud-shell"></a>Implantar do Azure Cloud Shell

É possível usar o [Azure Cloud Shell](https://shell.azure.com) para implantar o modelo. Para implantar um modelo externo, forneça o URI do modelo. Para implantar um modelo local, você deve carregar o modelo primeiro para a conta de armazenamento do seu Cloud Shell. Para carregar arquivos para o shell, selecione o ícone de menu **Carregar/Baixar arquivos** na janela do shell.

Para abrir o Cloud Shell, navegue até [https://shell.azure.com](https://shell.azure.com) ou selecione **Try-it** na seguinte seção de código:

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

Obtendo um valor de parâmetro de um arquivo é útil quando você precisa fornecer valores de configuração. Por exemplo, você pode fornecer [valores de cloud-init para uma máquina virtual Linux](../../virtual-machines/linux/using-cloud-init.md).

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

Para saber mais sobre o arquivo de parâmetro, confira [Criar arquivo de parâmetro do Resource Manager](parameter-files.md).

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

## <a name="next-steps"></a>Próximas etapas

- Para reverter para uma implantação bem-sucedida quando você receber um erro, confira [Reverter em caso de erro para uma implantação bem-sucedida](rollback-on-error.md).
- Para especificar como lidar com os recursos existentes no grupo de recursos, mas que não estão definidos no modelo, confira [Modos de implantação do Azure Resource Manager](deployment-modes.md).
- Para entender como definir parâmetros em seu modelo, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
- Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](secure-template-with-sas-token.md).
