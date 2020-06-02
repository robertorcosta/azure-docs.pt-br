---
title: 'Início Rápido: Gerar uma miniatura – REST, cURL'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você gerará uma miniatura de uma imagem usando a API da Pesquisa Visual Computacional com cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/20/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d6b55a7f3df7657e2b6b0b23b797d36788658f32
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745172"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Início Rápido: Gerar uma miniatura usando a API REST da Pesquisa Visual Computacional e o cURL

Neste início rápido, você gera uma miniatura de uma imagem usando a API REST da Pesquisa Visual Computacional. Você especifica a altura e largura desejadas, que podem diferir da imagem de entrada quanto à taxa de proporção. A Pesquisa Visual Computacional usa o corte inteligente para identificar a área de interesse de modo inteligente e gerar as coordenadas de corte em torno dessa região.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- É necessário ter [cURL](https://curl.haxx.se/windows).
- Você precisa ter uma chave de assinatura para a Pesquisa Visual Computacional. É possível obter uma chave de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar a Pesquisa Visual Computacional e obter sua chave.

## <a name="create-and-run-the-sample-command"></a>Criar e executar o comando de exemplo

Para criar e executar o exemplo, siga estas etapas:

1. Copie o seguinte comando em um editor de texto.
1. Faça as alterações a seguir no comando quando necessário:
    1. Substitua o valor de `<subscriptionKey>` pela sua chave de assinatura.
    1. Substitua o valor de `<thumbnailFile>` pelo caminho e o nome do arquivo no qual salvar a miniatura.
    1. Substitua a primeira parte da URL de solicitação (`westcentralus`) pelo texto em sua URL de ponto de extremidade.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcionalmente, altere a URL da imagem no corpo da solicitação (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) para a URL de uma imagem diferente da qual você deseja gerar uma miniatura.
1. Abra una janela de prompt de comando.
1. Cole o comando do editor de texto na janela do prompt de comando.
1. Pressione Enter para executar o programa.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta bem-sucedida grava a imagem em miniatura para o arquivo especificado em `<thumbnailFile>`. Se a solicitação falhar, a resposta conterá um código de erro e uma mensagem para ajudar a determinar o que deu errado. Se a solicitação parece ter êxito, mas a miniatura criada não for um arquivo de imagem válido, talvez sua chave de assinatura não seja válida.

## <a name="next-steps"></a>Próximas etapas

Explore a API da Pesquisa Visual Computacional para saber como analisar uma imagem, detectar celebridades e pontos de referência, criar uma miniatura e extrair textos manuscritos e impressos. Para testar rapidamente a API da Pesquisa Visual Computacional, experimente o [Abrir o console de teste de API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).

> [!div class="nextstepaction"]
> [Explorar a API da Pesquisa Visual Computacional](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b)
