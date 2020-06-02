---
title: Tutorial – Criar um workspace do Azure ML – modelo do Resource Manager
description: Neste tutorial, você usa um modelo do Azure Resource Manager para implantar rapidamente um workspace do Azure para aprendizado de máquina
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: b6eac711ed6c420330b036cb6094c841508cee9f
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790710"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-a-resource-manager-template"></a>Tutorial: Implantar um workspace do Azure Machine Learning usando um modelo do Resource Manager
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este tutorial mostrará como criar um workspace do Azure Machine Learning usando um modelo do Azure Resource Manager. Os workspaces do Azure Machine Learning organizam todos os seus ativos de machine learning, de conjuntos de linha de base a modelos implantados. Os workspaces são um local único para colaborar com colegas na criação, execução e revisão de experimentos, bem como para gerenciar seus recursos de computação de treinamento e inferência e para monitorar e controlar a versão dos modelos implantados.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar

* Para usar os comandos da CLI deste documento em seu **ambiente local**, você precisará da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>Criar um workspace

### <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json":::

Os seguintes recursos do Azure estão definidos no modelo:

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces): Criar um workspace do Azure ML. Neste modelo, o local e o nome são parâmetros que o usuário pode passar ou inserir interativamente.

### <a name="deploy-the-template"></a>Implantar o modelo 

Para usar o modelo por meio da CLI do Azure, faça logon e escolha sua assinatura (consulte [Entrar na CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Em seguida, execute:

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location && 
echo "Press [ENTER] to continue ..." &&
read
```

Ao executar o comando acima, digite:

1. Um nome de projeto que formará a base dos nomes do grupo de recursos criado e do workspace do Azure ML
1. O local do Azure no qual você deseja fazer a implantação

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Para ver seu workspace do Azure ML:

1. Acesse https://portal.azure.com 
1. Entrar 
1. Escolha o workspace que você acabou de criar

Você verá a home page do Azure Machine Learning: 

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Captura de tela do workspace do Azure ML":::

Para ver todos os recursos associados à implantação, clique no link no canto superior esquerdo com o nome do workspace (na captura de tela, `my_templated_ws`). Esse link leva você para o grupo de recursos no portal do Azure. O nome do grupo de recursos é `{projectName}rg` e o workspace é chamado de `{projectName}ws`.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser usar esse workspace, exclua-o. Como o workspace está associado a outros recursos, como uma conta de armazenamento, provavelmente será interessante excluir o grupo de recursos inteiro que você criou. Você pode excluir o grupo de recursos usando o portal e clicando no botão "Excluir" e confirmando. Ou, você pode excluir o grupo de recursos por meio da CLI com: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um workspace do Azure Machine Learning por meio de um modelo do Azure Resource Manager. Se você quiser explorar o Azure Machine Learning, continue com o tutorial. 

> [!div class="nextstepaction"]
> [Tutorial: Introdução à criação de seu primeiro experimento de ML com o SDK do Python](tutorial-1st-experiment-sdk-setup.md)
