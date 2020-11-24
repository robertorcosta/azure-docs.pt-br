---
title: 'Início rápido: Criar um índice de pesquisa usando APIs REST'
titleSuffix: Azure Cognitive Search
description: Neste início rápido da API REST, aprenda a chamar as APIs REST do Azure Cognitive Search usando o Postman ou o Visual Studio Code.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94711269"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Início rápido: Criar um índice do Azure Cognitive Search usando APIs REST

Este artigo explica como formular solicitações à API REST de maneira interativa usando as APIs REST do [Azure Cognitive Search](/rest/api/searchservice) e um cliente de API para enviar e receber solicitações. Com o cliente de API e essas instruções, você poderá enviar solicitações e ver as respostas antes da codificação.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Os recursos mantidos em execução podem gerar custos. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como executar tarefas básicas, pode avançar com chamadas adicionais à API REST para obter recursos mais avançados, como indexadores ou [configurar um pipeline de enriquecimento](cognitive-search-tutorial-blob.md) que adicione transformações de conteúdo para indexação. Para a próxima etapa, recomendamos o seguinte link:

> [!div class="nextstepaction"]
> [Tutorial: Usar o REST e a IA para gerar conteúdo pesquisável com base em blobs do Azure](cognitive-search-tutorial-blob.md)