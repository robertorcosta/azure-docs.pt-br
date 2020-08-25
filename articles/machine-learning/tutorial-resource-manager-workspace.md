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
ms.openlocfilehash: 760406b738d2aa95ef086941850814f4bf39fbb4
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639861"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-an-arm-template"></a>Tutorial: Implantar um workspace do Azure Machine Learning usando um modelo do Resource Manager

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este tutorial mostrará como criar um workspace do Azure Machine Learning usando um modelo do ARM (Azure Resource Manager). Os workspaces do Azure Machine Learning organizam todos os seus ativos de machine learning, de conjuntos de linha de base a modelos implantados. Os workspaces são um local único para colaborar com colegas na criação, execução e revisão de experimentos, bem como para gerenciar seus recursos de computação de treinamento e inferência e para monitorar e controlar a versão dos modelos implantados.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-machine-learning-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/services/machine-learning/) antes de começar.

* Para usar os comandos da CLI deste documento em seu **ambiente local**, você precisará da [CLI do Azure](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json":::

Os seguintes recursos do Azure estão definidos no modelo:

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces): Criar um workspace do Azure ML. Neste modelo, o local e o nome são parâmetros que o usuário pode passar ou inserir interativamente.

## <a name="deploy-the-template"></a>Implantar o modelo

Para usar o modelo por meio da CLI do Azure, faça logon e escolha sua assinatura (consulte [Entrar na CLI do Azure](/cli/azure/authenticate-azure-cli)). Em seguida, execute:

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

1. Um nome de projeto que formará a base dos nomes do grupo de recursos criado e do workspace do Azure ML.
1. A localização do Azure na qual você deseja fazer a implantação.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Para ver seu workspace do Azure ML:

1. Ir para https://portal.azure.com.
1. Entrar.
1. Escolha o workspace recém-criado.

Você verá a home page do Azure Machine Learning:

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Captura de tela do workspace do Azure ML":::

Para ver todos os recursos associados à implantação, clique no link no canto superior esquerdo com o nome do workspace (na captura de tela, `my_templated_ws`). Esse link leva você para o grupo de recursos no portal do Azure. O nome do grupo de recursos é `{projectName}rg` e o workspace é chamado de `{projectName}ws`.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser usar esse workspace, exclua-o. Como o workspace está associado a outros recursos, como uma conta de armazenamento, provavelmente será interessante excluir o grupo de recursos inteiro que você criou. Exclua o grupo de recursos por meio do portal clicando no botão **Excluir** e confirmando a ação. Ou, você pode excluir o grupo de recursos por meio da CLI com:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um workspace do Azure Machine Learning por meio de um modelo do Resource Manager. Se você quiser explorar o Azure Machine Learning, continue com o tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Introdução à criação de seu primeiro experimento de ML com o SDK do Python](tutorial-1st-experiment-sdk-setup.md)
