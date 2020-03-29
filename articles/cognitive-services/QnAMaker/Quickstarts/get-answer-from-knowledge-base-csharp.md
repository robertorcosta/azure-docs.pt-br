---
title: 'Início Rápido: Obter respostas da base de dados de conhecimento - REST, C# - QnA Maker'
description: Este início rápido de C# baseado em REST fornece uma orientação para obtenção de uma resposta de uma base de dados de conhecimento de forma programática.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 16093ec5e837b098da3c9b038fe2a57cd76c7151
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851801"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Quickstart: Obtenha respostas para uma pergunta de uma base de conhecimento com C #

Este início rápido o orienta a obter programaticamente uma resposta de uma base de dados de conhecimento publicada do QnA Maker. A base de conhecimento contém perguntas e respostas de [fontes de dados](../Concepts/knowledge-base.md), como perguntas frequentes. A [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada para o serviço QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta prevista na parte superior.

[Amostra de documentação de](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [referência](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs)

## <a name="prerequisites"></a>Pré-requisitos

* [**Edição mais recente do Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar sua chave, selecione **Chaves** em **Gerenciamento de Recursos** no painel do Azure para seu recurso do QnA Maker.
* Configurações da página **Publicar**. Caso não tenha uma base de dados de conhecimento publicada, crie uma base de dados de conhecimento vazia, importe-a na página **Configurações** e depois publique-a. Você pode baixar e usar [essa base de dados de conhecimento básico](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    As configurações da página de publicação incluem o valor da rota, o valor do Host e o valor do EndpointKey.

    ![Configurações de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-knowledge-base-project"></a>Criar um projeto da base de dados de conhecimento

1. Abra o Visual Studio 2019 Community Edition.
1. Crie um novo projeto de Aplicativo de Console (.NET Core) e nomeie-o QnaMakerQuickstart. Aceite os padrões para as configurações restantes.

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Na parte superior do arquivo Program.cs, substitua a única instrução using pelas linhas abaixo para adicionar as dependências necessárias ao projeto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior da classe `Program`, dentro do `Main`, adicione as constantes necessárias para acessar o QnA Maker. Esses valores ficam na página **Publicar** após a publicação da base de dados de conhecimento.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=9-41 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Adicionar uma solicitação POST para enviar perguntas e obter uma resposta

O código a seguir faz uma solicitação HTTPS à API de QnA Maker para enviar a pergunta à base de dados de conhecimento e recebe a resposta:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=43-76 "Add a POST request to send question to knowledge base")]

O valor do cabeçalho de `Authorization` inclui a cadeia de caracteres `EndpointKey`.

Saiba mais sobre a [solicitação](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e a [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compilar e executar o programa no Visual Studio. Ela enviará automaticamente a solicitação para a API de QnA Maker, depois a resposta será impressa na janela do console.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)