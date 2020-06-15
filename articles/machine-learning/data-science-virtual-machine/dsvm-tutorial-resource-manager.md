---
title: 'Início Rápido: Criar uma VM de Ciência de Dados – Modelo do Resource Manager'
titleSuffix: Azure Data Science Virtual Machine
description: Neste guia de início rápido, você usa um modelo de Azure Resource Manager para implantar rapidamente um Máquina Virtual de Ciência de Dados
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: machine-learning
ms.subservice: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: e89f3d85156081106f4e1dbb55ee6c895e7e7a2d
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636743"
---
# <a name="tutorial-create-an-ubuntu-data-science-virtual-machine-using-a-resource-manager-template"></a>Tutorial: Criar uma Máquina Virtual de Ciência de Dados do Ubuntu usando um modelo do Resource Manager
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este guia de início rápido mostra como criar uma Máquina Virtual de Ciência de Dados do Ubuntu 18.04 usando um modelo do Azure Resource Manager. As Máquinas Virtuais de Ciência de Dados são máquinas virtuais baseadas em nuvem pré-carregadas com um conjunto de estruturas e ferramentas de ciência de dados e de machine learning. Quando implantadas em recursos de computação baseados em GPU, todas as ferramentas e bibliotecas são configuradas para usar a GPU. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar

* Para usar os comandos da CLI deste documento em seu **ambiente local**, você precisará da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>Criar um workspace

### <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/). O modelo completo deste artigo é muito longo para ser mostrado aqui. Para ver o modelo completo, confira [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json). A parte que define as especificidades da DSVM é mostrada aqui:

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" range="235-276":::

Os seguintes recursos do Azure estão definidos no modelo:

* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Criar uma máquina virtual baseada em nuvem. Neste modelo, a máquina virtual está configurada como uma Máquina Virtual de Ciência de Dados executando o Ubuntu 18.04.

### <a name="deploy-the-template"></a>Implantar o modelo 

Para usar o modelo por meio da CLI do Azure, faça logon e escolha sua assinatura (consulte [Entrar na CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Em seguida, execute:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey && 
echo "Press [ENTER] to continue ..." &&
read
```

Ao executar o comando acima, digite:

1. O nome do grupo de recursos que você deseja criar para conter a DSVM e os recursos associados. 
1. O local do Azure no qual você deseja fazer a implantação
1. O tipo de autenticação que você gostaria de usar (insira a cadeia de caracteres `password` ou `sshPublicKey`)
1. O nome de logon da conta de administrador (esse valor pode não ser `admin`)
1. O valor da senha ou da chave pública SSH da conta

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Para ver sua Máquina Virtual de Ciência de Dados:

1. Acesse https://portal.azure.com 
1. Entrar 
1. Escolha o grupo de recursos que você acabou de criar

Você verá as informações do grupo de recursos: 

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Captura de tela de um grupo de recursos básico que contém uma DSVM":::

Clique no recurso da Máquina Virtual para ir para sua página de informações. Aqui, você pode encontrar informações sobre a VM, incluindo detalhes da conexão. 

## <a name="clean-up-resources"></a>Limpar os recursos

Se não quiser usar essa máquina virtual, você poderá exclui-la. Como a DSVM está associada a outros recursos, como uma conta de armazenamento, provavelmente será interessante excluir o grupo de recursos inteiro que você criou. Você pode excluir o grupo de recursos usando o portal e clicando no botão "Excluir" e confirmando. Ou, você pode excluir o grupo de recursos por meio da CLI com: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma Máquina Virtual de Ciência de Dados usando um modelo do Azure Resource Manager. 

> [!div class="nextstepaction"]
> [Exemplos de programas e instruções de ML](dsvm-samples-and-walkthroughs.md)
