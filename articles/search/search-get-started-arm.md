---
title: Usar um modelo do Azure Resource Manager para implantar seu serviço
titleSuffix: Azure Cognitive Search
description: Implante rapidamente uma instância do serviço Azure Cognitive Search usando o modelo do Azure Resource Manager.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80611810"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Início Rápido: Implantar o Cognitive Search usando um modelo do Resource Manager

Este artigo descreve o processo de uso de um modelo do Resource Manager para implantar um recurso do Azure Cognitive Search no portal do Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-cognitive-search-service"></a>Criar um serviço do Cognitive Search

### <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [modelos do Azure](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

O recurso do Azure definido neste modelo:

- [Microsoft.Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): cria um serviço do Azure Cognitive Search

### <a name="deploy-the-template"></a>Implantar o modelo

Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria um recurso do Azure Cognitive Search.

[![Implantar no Azure](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

O portal exibe um formulário que permite fornecer valores de parâmetro com facilidade. Alguns parâmetros são preenchidos previamente com os valores padrão do modelo. Você precisará fornecer a assinatura, o grupo de recursos, a localização e o nome do serviço. Caso deseje usar os Serviços Cognitivos em um pipeline de [enriquecimento de IA](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro), por exemplo, para analisar o texto de arquivos de imagem binária, escolha uma localização que ofereça o Cognitive Search e os Serviços Cognitivos. Os dois serviços devem estar na mesma região para cargas de trabalho de enriquecimento de IA. Depois de completar o formulário, você precisará concordar com os termos e as condições e, em seguida, selecionar o botão Comprar para concluir a implantação.

> [!div class="mx-imgBorder"]
> ![Exibição do modelo no portal do Azure](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Quando a implantação for concluída, você poderá acessar o novo grupo de recursos e o novo serviço de pesquisa no portal.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais e guias de início rápido do Cognitive Search complementam este início rápido. Se você pretende continuar trabalhando com os tutoriais e os guias de início rápido a seguir, o ideal é manter esse recurso em vigor. Quando ele não for mais necessário, você poderá excluir o grupo de recursos, o que exclui o serviço do Cognitive Search e os recursos relacionados.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um serviço do Cognitive Search usando um modelo do Azure Resource Manager e validou a implantação. Para saber mais sobre o Cognitive Search e o Azure Resource Manager, prossiga para os artigos abaixo.

 - Leia uma [visão geral do Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Criar um índice](https://docs.microsoft.com/azure/search/search-get-started-portal) para o serviço de pesquisa
 - [Criar um aplicativo de pesquisa](https://docs.microsoft.com/azure/search/search-create-app-portal) usando o assistente do portal
 - [Criar um conjunto de habilidades](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) para extrair informações dos seus dados


