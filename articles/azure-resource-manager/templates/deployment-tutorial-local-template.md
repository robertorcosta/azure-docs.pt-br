---
title: Tutorial – Implantar o modelo do Azure Resource Manager local
description: Aprenda a implantar um modelo do Azure Resource Manager do seu computador local
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7f134bb836d05d006ef2e474ea48382a671957fe
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82188817"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>Tutorial: Implantar o modelo do Azure Resource Manager local

Aprenda a implantar um modelo do Azure Resource Manager do seu computador local. Esse procedimento demora cerca de **8 minutos** para ser concluído.

Este tutorial é o primeiro de uma série. À medida que avança na série, você modulariza o modelo criando um modelo vinculado, armazena o modelo vinculado em uma conta de armazenamento e protege o modelo vinculado usando o token SAS, além de aprender a criar um pipeline DevOp para implantar modelos. Esta série se concentra na implantação de modelo.  Se desejar aprender sobre o desenvolvimento de modelos, confira os [tutoriais de iniciantes](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Obter ferramentas

Vamos começar verificando se você tem as ferramentas necessárias para implantar modelos.

### <a name="command-line-deployment"></a>Implantação de linha de comando

Você precisa do Azure PowerShell ou da CLI do Azure para implantar o modelo. Para obter as instruções de instalação, confira:

- [Instale o Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
- [Instalar a CLI do Azure no Linux](/cli/azure/install-azure-cli-linux)

Depois de instalar o Azure PowerShell ou a CLI do Azure, é preciso que você entre pela primeira vez. Para obter ajuda, confira [Entrar – PowerShell](/powershell/azure/install-az-ps#sign-in) ou [Entrar – CLI do Azure](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Editor (opcional)

Modelos são arquivos JSON. Para examinar/editar modelos, você precisa de um bom editor de JSON. Recomendamos o Visual Studio Code com a extensão Ferramentas do Resource Manager. Se você precisar instalar essas ferramentas, confira [Usar o Visual Studio Code para criar modelos do Azure Resource Manager](use-vs-code-to-create-template.md).

## <a name="review-template"></a>Examinar modelo

O modelo implanta uma conta de armazenamento, um plano do serviço de aplicativo e um aplicativo Web. Se você estiver interessado em criar o modelo, veja o [tutorial sobre Modelos de Início Rápido](template-tutorial-quickstart-template.md). No entanto, isso não é necessário para concluir este tutorial.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres, usar números e apenas letras minúsculas. O nome deve ser exclusivo. No modelo, o nome da conta de armazenamento é o nome do projeto com "armazenamento" acrescentado, e o nome do projeto deve ter entre 3 e 11 caracteres. Portanto, o nome do projeto deve atender aos requisitos de nome da conta de armazenamento e ter menos de 11 caracteres.

Salve uma cópia do modelo em seu computador local com a extensão .json, por exemplo, azuredeploy.json. Você implantará esse modelo posteriormente no tutorial.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para começar a trabalhar com o Azure PowerShell/a CLI do Azure para implantar um modelo, entre com suas credenciais do Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az login
```

---

Se você tiver várias assinaturas do Azure, selecione a que deseja usar:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Select-AzSubscription [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Criar grupo de recursos

Ao implantar um modelo, você especificará um grupo de recursos que conterá os recursos. Antes de executar o comando de implantação, crie o grupo de recursos com a CLI do Azure ou o Azure PowerShell. Selecione as guias na seção de código a seguir para escolher entre o Azure PowerShell e a CLI do Azure. Os exemplos de CLI neste artigo são escritos para o shell do Bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Implantar modelo

Use uma ou ambas as opções de implantação para implantar o modelo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Para saber mais sobre como implantar o modelo usando o Azure PowerShell, confira [Implantar recursos com modelos do Resource Manager e com o Azure PowerShell](./deploy-powershell.md).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Para saber mais sobre como implantar o modelo usando a CLI do Azure, confira [Implantar recursos com modelos do Resource Manager e com a CLI do Azure](./deploy-cli.md).

---

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos que você implantou excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a implantar um modelo local. No próximo tutorial, separe o modelo em um modelo principal e um modelo vinculado e saiba como armazenar e proteger este último.

> [!div class="nextstepaction"]
> [Implantar um modelo vinculado](./deployment-tutorial-linked-template.md)
