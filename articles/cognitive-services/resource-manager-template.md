---
title: 'Início Rápido: Criar um recurso dos Serviços Cognitivos do Azure usando modelos do ARM | Microsoft Docs'
description: Introdução ao uso de um modelo do Azure Resource Manager para implantar um recurso dos Serviços Cognitivos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 06/18/2020
ms.author: aahi
ms.custom: subject-armqs
ms.openlocfilehash: fbaa10ec8a0b48d40644bb90144d89f53ab52563
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2020
ms.locfileid: "85513534"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>Início Rápido: Criar um recurso dos Serviços Cognitivos usando um modelo do ARM

Use este artigo para criar e implantar um recurso dos Serviços Cognitivos usando um modelo do ARM (Azure Resource Manager). Este recurso de vários serviços permite:
* Acessar vários Serviços Cognitivos do Azure com uma chave e um ponto de extremidade.
* Reúna todos os serviços que você usa em uma cobrança.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos do ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services).

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/).

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json" highlight="27-41":::

Um recurso do Azure é definido no modelo:
* [Microsoft.CognitiveServices/accounts](https://docs.microsoft.com/azure/templates/microsoft.cognitiveservices/accounts): cria um recurso dos Serviços Cognitivos.

## <a name="deploy-the-template"></a>Implantar o modelo

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Clique no botão **Implantar no Azure**.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Insira os valores a seguir.
    
    |Valor  |Descrição  |
    |---------|---------|
    | **Assinatura** | Selecione uma assinatura do Azure. |
    | **Grupo de recursos** | Selecione **Criar**, insira um nome exclusivo para o grupo de recursos e clique em **OK**. |
    | **Região** | Selecione uma região.  Por exemplo, **Leste dos EUA** |
    | **Nome do Serviço Cognitivo** | Substitua por um nome exclusivo para seu recurso. Você precisará do nome na próxima seção ao validar a implantação. |
    | **Localidade** | Substitua pela região usada acima. |
    | **Sku** | O [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/) do seu recurso. |
    
    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="Tela de criação do recurso.":::

3. Selecione **Examinar + Criar** e **Criar**. Depois que o recurso tiver concluído a implantação com êxito, o botão **Ir para o recurso** estará realçado.


# <a name="azure-cli"></a>[CLI do Azure](#tab/CLI)

> [!NOTE]
> A criação do `az deployment group` requer a CLI do Azure versão 2.6 ou mais recente. Para exibir a versão, digite `az --version`. Para obter mais informações, confira a [documentação](https://docs.microsoft.com/cli/azure/deployment/group).

Execute o script a seguir usando a CLI (interface de linha de comando) do Azure [em seu computador local](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou em um navegador com o botão **Experimentar**. Insira um nome e uma localização (por exemplo, `centralus`) para um novo grupo de recursos e o modelo do ARM será usado para implantar um recurso dos Serviços Cognitivos dentro dele. Lembre-se do nome que você usar. Você o usará posteriormente para validar a implantação.


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---


## <a name="validate-the-deployment"></a>Validar a implantação

# <a name="portal"></a>[Portal](#tab/portal)

Quando a implantação for concluída, você poderá clicar no botão **Ir para o recurso** para ver o novo recurso. Também é possível encontrar o grupo de recursos:

1. Selecionando **Grupos de recursos** no menu de navegação esquerdo.
2. Selecionando o nome do grupo de recursos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/CLI)

Usando a CLI do Azure, execute o script a seguir e insira o nome do grupo de recursos criado anteriormente.

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui outros recursos contidos nele.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **Grupo de Recursos** para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos que contém o recurso a ser excluído
3. Clique com o botão direito do mouse na listagem do grupo de recursos. Selecione **Excluir grupo de recursos** e confirme.

# <a name="azure-cli"></a>[CLI do Azure](#tab/CLI)

Usando a CLI do Azure, execute o script a seguir e insira o nome do grupo de recursos criado anteriormente. 

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Próximas etapas

* [Autenticar solicitações para os Serviços Cognitivos do Azure](authentication.md)
* [O que são os Serviços Cognitivos do Azure?](Welcome.md)
* [Suporte para idioma natural](language-support.md)
* [Suporte ao contêiner do Docker](cognitive-services-container-support.md)
