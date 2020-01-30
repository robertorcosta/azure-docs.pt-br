---
title: Tutorial – usar um arquivo de parâmetro para implantar o modelo
description: Use arquivos de parâmetro que contenham os valores a serem usados para implantar seu modelo do Azure Resource Manager.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6a12d92c0cfb9d86ebf4c335c351944997f79b4e
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773157"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>Tutorial: Use arquivos de parâmetro para implantar seu modelo do Resource Manager

Neste tutorial, você aprenderá a usar os [arquivos de parâmetro](parameter-files.md) para armazenar os valores que você passa durante a implantação. Nos tutoriais anteriores, você usou parâmetros embutidos com seu comando de implantação. Essa abordagem funcionou para testar seu modelo, mas, ao automatizar implantações, pode ser mais fácil passar um conjunto de valores para seu ambiente. Os arquivos de parâmetro tornam mais fácil empacotar valores de parâmetro para um ambiente específico. Neste tutorial, você criará arquivos de parâmetro para ambientes de desenvolvimento e produção. Esse procedimento demora cerca de **12 minutos** para ser concluído.

## <a name="prerequisites"></a>Prerequisites

Recomendamos que você conclua o [tutorial sobre marcas](template-tutorial-add-tags.md), mas isso não é obrigatório.

É necessário ter o Visual Studio Code com a extensão Ferramentas do Resource Manager e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Examinar modelo

Seu modelo tem muitos parâmetros que você pode fornecer durante a implantação. No final do tutorial anterior, seu modelo tinha a seguinte aparência:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Esse modelo funciona bem, mas agora convém gerenciar facilmente os parâmetros que você passa para o modelo.

## <a name="add-parameter-files"></a>Adicionar arquivos de parâmetro

Os arquivos de parâmetro são arquivos JSON com uma estrutura semelhante ao seu modelo. No arquivo, você fornece os valores de parâmetro que você deseja passar durante a implantação.

No VS Code, crie um arquivo com o seguinte conteúdo. Salve o arquivo com o nome **azuredeploy.parameters.dev.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Esse arquivo é seu arquivo de parâmetro para o ambiente de desenvolvimento. Observe que ele usa Standard_LRS para a conta de armazenamento, nomeia recursos com um prefixo **dev** e define a tag **Ambiente** como **Dev**.

Novamente, crie um arquivo com o conteúdo a seguir. Salve o arquivo com o nome **azuredeploy.parameters.prod.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Esse arquivo é seu arquivo de parâmetro para o ambiente de produção. Observe que ele usa Standard_GRS para a conta de armazenamento, nomeia recursos com um prefixo **contoso** e define uma tag **Ambiente** como **Produção**. Em um ambiente de produção real, também seria conveniente usar um serviço de aplicativo com um SKU que não seja gratuito, mas continuaremos usando esse SKU para este tutorial.

## <a name="deploy-template"></a>Implantar modelo

Use a CLI do Azure ou o Azure PowerShell para implantar o modelo.

Como um teste final do seu modelo, vamos criar dois novos grupos de recursos. Um para o ambiente de desenvolvimento e outro para o ambiente de produção.

Primeiro, implantaremos no ambiente de desenvolvimento.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az group deployment create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

Agora, implantaremos no ambiente de produção.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az group deployment create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

## <a name="verify-deployment"></a>Verificar implantação

Você pode verificar a implantação explorando os grupos de recursos do portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Grupos de recursos**.
1. Você verá os dois novos grupos de recursos que você implantou neste tutorial.
1. Selecione grupo de recursos e exiba os recursos implantados. Observe que eles correspondem aos valores especificados no seu arquivo de parâmetro para esse ambiente.

## <a name="clean-up-resources"></a>Limpar os recursos

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos. Se tiver concluído essa série, você terá três grupos de recursos para excluir – myResourceGroup, myResourceGroupDev e myResourceGroupProd.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Parabéns, você concluiu esta introdução à implantação de modelos no Azure. Informe-nos se você tiver comentários e sugestões na seção de comentários. Obrigado!

Você está pronto para acessar conceitos mais avançados sobre modelos. O próximo tutorial detalha mais o uso da documentação de referência do modelo para ajudar na definição de recursos a serem implantados.

> [!div class="nextstepaction"]
> [Utilizar a referência de modelo](template-tutorial-create-encrypted-storage-accounts.md)
