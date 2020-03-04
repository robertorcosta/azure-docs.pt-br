---
title: Obter o modelo com chamadas REST em C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2020
ms.author: diberry
ms.openlocfilehash: 4d8da7d2bc51c4fc4ebc8d71f230f24f20b3aa24
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368409"
---
## <a name="prerequisites"></a>Prerequisites

* Reconhecimento vocal do Azure – Criar chave de 32 caracteres do recurso e a URL de ponto de extremidade. Crie com o [portal do Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ou com a [CLI do Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importe o aplicativo [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) do repositório GitHub cognitive-services-language-understanding.
* A ID do aplicativo LUIS para o aplicativo TravelAgent importado. A ID do aplicativo é mostrada no painel do aplicativo.
* A ID da versão no aplicativo que recebe os enunciados. A ID padrão é “0.1”.
* [Python 3.6](https://www.python.org/downloads/) ou posterior.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Exemplo de arquivo JSON de enunciados

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Alterar o modelo de maneira programática

1. Crie um arquivo chamado `model.py`. Adicione os códigos a seguir:

    [!code-python[Add example utterances to Language Understanding in python](~/samples-luis/documentation-samples/quickstarts/change-model/python/3.x/add-utterances-3-6.py)]

1. Substitua os valores que começam com `YOUR-` por seus valores.

    |Informações|Finalidade|
    |--|--|
    |`YOUR-KEY`|Sua chave de criação de 32 caracteres.|
    |`YOUR-ENDPOINT`| Seu ponto de extremidade da URL de criação. Por exemplo, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Você definiu o nome do recurso quando você criou o recurso.|
    |`YOUR-APP-ID`| Sua ID do aplicativo LUIS. |

    As chaves e os recursos atribuídos estão visíveis no portal do LUIS na seção Gerenciar, na página **recursos do Azure**. A ID do aplicativo está disponível na mesma seção Gerenciar, na página **Configurações do Aplicativo**.

1. Com um prompt de comando no mesmo diretório em que você criou o arquivo, insira o seguinte comando para executar o arquivo:

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Ao concluir este guia de início rápido, exclua o arquivo do sistema de arquivos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Práticas recomendadas para um aplicativo](../luis-concept-best-practices.md)
