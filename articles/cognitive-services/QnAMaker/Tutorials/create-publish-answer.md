---
title: 'Tutorial: Criar, publicar e responder – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este tutorial baseado em REST guiará você pela criação e publicação programáticas de uma base de dados de conhecimento e pela resposta a uma pergunta com a base de dados de conhecimento.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: f0888b25258f6a7830df1195995159432b19907d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802819"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>Tutorial: Usando C#, criar a base de dados de conhecimento e responder à pergunta

Este tutorial guiará você pela criação e publicação programáticas de uma KB (base de dados de conhecimento) e pela resposta a uma pergunta do cliente com a base de dados de conhecimento. 

> [!div class="checklist"]
> * Como criar uma base de dados de conhecimento 
> * Verificar status de criação
> * Treinar e publicar a base de dados de conhecimento
> * Obter informações do ponto de extremidade
> * Usar cURL para consultar a base de dados de conhecimento


Este início rápido chama as APIs REST do QnA Maker:

* [Criar KB (Base de dados de conhecimento)](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Obter detalhes da operação](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)
* [Obter detalhes da Base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails) 
* [Obter pontos de extremidade da Base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys)
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) 

## <a name="prerequisites"></a>Pré-requisitos

* [**Edição mais recente do Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o nome do recurso, selecione **Início Rápido** no portal do Azure para o recurso do QnA Maker. 

> [!NOTE] 
> Os arquivos da solução completa estão disponíveis no repositório do GitHub [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="create-a-knowledge-base-project"></a>Criar um projeto da base de dados de conhecimento

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

No topo do Program.cs, substitua a única instrução _using_ pelas linhas abaixo para adicionar as dependências necessárias ao projeto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>Adicione uma classe KBDetails
Adicione essa classe KBDetails dentro dos colchetes de Namespace. Essa classe permite que a biblioteca NewtonSoft desserialize a resposta JSON em uma classe C#.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

No topo da classe Program, adicione as constantes a seguir para acessar o QnA Maker:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Adicionar a definição da base de dados de conhecimento

Após as constantes, adicione a seguinte definição de modelo da base de dados de conhecimento:

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>Adicionar funções e estruturas de suporte
Adicione o seguinte bloco de código dentro da classe Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Adicionar uma solicitação POST para criar a base de dados de conhecimento

O código a seguir faz uma solicitação HTTPS para a API de QnA Maker para criar uma base de dados de conhecimento e recebe a resposta:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

Essa chamada à API retorna uma resposta JSON que inclui a ID da operação. Mais tarde, o programa usará a ID da operação para determinar se a base de dados de conhecimento foi criada com êxito. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Adicionar solicitação GET para determinar o status da criação

Verifique o status da operação de criação.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

Essa chamada à API retorna uma resposta JSON que inclui o status da operação: 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

Repita a chamada até ter um resultado com êxito ou falha: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Adicionar o método CreateKB

O método a seguir encapsula as chamadas para criar a base de dados de conhecimento e verificar o status.  A _ID da Operação_**de criação** é retornada no **Local** do campo de cabeçalho da resposta POST e, em seguida, usada como parte da rota na solicitação GET. Como a criação de KB pode levar algum tempo, você precisará repetir a chamadas para verificar o status até que o status indique êxito ou falha. Quando a operação é bem-sucedida, a ID da base de dados de conhecimento é retornada em **resourceLocation**. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>Adicionar método de publicação

Depois que a base de dados de conhecimento for criada com êxito, publique-a. Talvez você estivesse esperando uma chamada a uma API de treinamento. Isso não é necessário com esta versão. 

O código a seguir faz uma solicitação HTTPS para a API de QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

A chamada à API retorna um status 204 de publicação bem-sucedida sem nenhum conteúdo no corpo da resposta. O código de início rápido adiciona texto de 204 respostas para que você possa ver o resultado.

Para qualquer outra resposta, essa resposta é retornada inalterada.

## <a name="generating-an-answer"></a>Gerando uma resposta
Para acessar a KB para enviar uma pergunta e receber a melhor resposta, o programa precisará do _nome do recurso_ da API de detalhes da KB e da _chave do ponto de extremidade primário_ da API de Pontos de Extremidade. Esses métodos estão nas seções a seguir, junto com o método para gerar uma resposta. 

A tabela abaixo ilustra como os dados são usados para construir o URI:

|Gerar modelo de URI de resposta|
|--|
|https://**nome-do-recurso**.azurewebsites.net/qnamaker/knowledgebases/**KBID**/generateAnswer|

O _ponto de extremidade primário_ é passado como um cabeçalho para autenticar a solicitação e gerar uma resposta:

|Nome do cabeçalho|Valor do cabeçalho|
|--|--|
|Autorização|`Endpoint` + **ponto de extremidade primário**<br>Exemplo: `Endpoint xxxxxxx`<br>Observe o espaço entre o texto do `Endpoint` e o valor do ponto de extremidade primário. 

O corpo da solicitação precisa passar o JSON correto:

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>Obter detalhes da base de dados de conhecimento
Adicione o seguinte método para obter os detalhes da base de dados de conhecimento. Esses detalhes contêm o nome do recurso da KB, conhecido como `hostName` no JSON a seguir. O nome do recurso é o nome do recurso do QnA Maker que você inseriu ao criar o recurso do QnA Maker. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

Essa chamada à API retorna uma resposta JSON: 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>Obter pontos de extremidade da base de dados de conhecimento
Adicione o método a seguir para obter os pontos de extremidade primários do QnA Maker. Esses pontos de extremidade não estão vinculados à base de dados de conhecimento; eles são válidos para todas as bases de dados de conhecimento associadas às chaves de recurso do QnA Maker no portal do Azure.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

Essa chamada à API retorna uma resposta JSON: 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>Obter uma resposta
Adicione o método a seguir para obter uma resposta para a pergunta do usuário. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

Essa chamada à API retorna uma resposta JSON: 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>Método Principal
O método principal mostra as chamadas síncronas para criar, publicar e gerar a resposta. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compile e execute o programa. 

Depois que sua base de dados de conhecimento tiver sido criada, você poderá exibi-la em seu Portal do QnA Maker, página [Minhas bases de dados de conhecimento](https://www.qnamaker.ai/Home/MyServices). Depois de aprender a usar a API de geração de respostas, você poderá usar a API em qualquer linguagem ou estrutura de solicitação HTTP. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
