---
title: Obter a intenção com chamada REST em Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 7199dfaaa476e4be27929010b76a6e0544857bdb
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838482"
---
## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) ou seu IDE favorito
* ID do aplicativo público: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Obter a intenção de forma programática

Você pode usar o Java para acessar os mesmos resultados que você viu na janela do navegador na etapa anterior. Lembre-se de adicionar as bibliotecas do Apache ao projeto.

1. Copie o código a seguir para criar uma classe em um arquivo chamado `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Substitua o valor da variável `YOUR-KEY` pela sua chave do LUIS.

3. Substitua isso pelo caminho do arquivo e compile o programa Java em uma linha de comando: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Substitua isso pelo caminho do arquivo e execute o aplicativo em uma linha de comando: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Ele exibe o mesmo JSON que você viu anteriormente na janela do navegador.

    ![A janela de console exibe resultados JSON do LUIS](../media/luis-get-started-java-get-intent/console-turn-on.png)
    


## <a name="luis-keys"></a>Chaves de LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com este início rápido, feche o projeto do Visual Studio e remova o diretório do projeto do sistema de arquivos. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar enunciados e treinar com Java](../luis-get-started-java-add-utterance.md)