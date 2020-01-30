---
title: 'Início Rápido: Criar base de conhecimento – REST, C# – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este início rápido baseado em REST de C# orienta você pela criação programática de um exemplo de base de dados de conhecimento do QnA Maker, que será exibida no Painel do Azure de sua conta da API dos Serviços Cognitivos.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: dd31d74319148961fd14ea48beaa5053f0a4b9e5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844289"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>Início Rápido: Criar uma base de dados de conhecimento no QnA Maker usando o C# com REST

Este início rápido ensina a criar e a publicar uma base de dados de conhecimento do QnA Maker de maneira programática. O QnA Maker extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como perguntas frequentes, de [fontes de dados](../Concepts/knowledge-base.md). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo da solicitação da API.

Este início rápido chama as APIs de QnA Maker:
* [Criar uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Obter detalhes da operação](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Exemplo de C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisites

* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Você precisa ter um [recurso do QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **Início Rápido** para o recurso no portal do Azure.

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Crie um novo aplicativo .NET Core em seu IDE ou editor preferido.

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `qna-maker-quickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```dotnetcli
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

No topo do Program.cs, substitua a única instrução using pelas linhas abaixo para adicionar as dependências necessárias ao projeto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior da classe Program, adicione as constantes necessárias para acessar o QnA Maker.

Defina as seguintes variáveis em variáveis de ambiente:

* `QNA_MAKER_SUBSCRIPTION_KEY`– A **chave** é uma cadeia de 32 caracteres e está disponível no portal do Azure, no recurso QnA Maker, na página Início rápido. Isso não é o mesmo que a chave de ponto de extremidade de previsão.
* `QNA_MAKER_ENDPOINT` – O **ponto de extremidade** é a URL para a criação, no formato de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Essa não é a mesma URL usada para consultar o ponto de extremidade de previsão.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=17-26 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Adicionar a definição da base de dados de conhecimento

Após as constantes, adicione a seguinte definição de base de dados de conhecimento:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=28-58 "Add the knowledge base definition")]

## <a name="add-supporting-functions-and-structures"></a>Adicionar funções e estruturas de suporte
Adicione o seguinte bloco de código dentro da classe Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=60-99 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Adicionar uma solicitação POST para criar a base de dados de conhecimento

O código a seguir faz uma solicitação HTTPS para a API de QnA Maker para criar uma base de dados de conhecimento e recebe a resposta:

[!code-csharp[Add PostCreateKB to request via POST](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=145-165 "Add PostCreateKB to request via POST")]

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=101-122 "Add a POST request to create KB")]

Essa chamada à API retorna uma resposta JSON que inclui a ID da operação. Use a ID da operação para determinar se a base de dados de conhecimento foi criada com êxito.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Adicionar solicitação GET para determinar o status da criação

Verifique o status da operação.

[!code-csharp[Add GetStatus to request via GET](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=167-187 "Add GetStatus to request via GET")]

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=124-143 "Add GET request to determine creation status")]

Essa chamada à API retorna uma resposta JSON que inclui o status da operação:

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Adicionar o método CreateKB

O método a seguir cria a base de dados de conhecimento e repete as verificações do status.  A **ID da Operação** de _criação_ é retornada na **Localização** do campo de cabeçalho da resposta POST e, em seguida, usada como parte da rota na solicitação GET. Como a criação de KB pode levar algum tempo, você precisará repetir a chamadas para verificar o status até que o status indique êxito ou falha. Quando a operação é bem-sucedida, a ID da base de dados de conhecimento é retornada em **resourceLocation**.

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=189-254 "Add CreateKB method")]

## <a name="add-the-createkb-method-to-main"></a>Adicionar o método CreateKB ao Main

Altere o método Main para chamar o método CreateKB:

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=256-265 "Add CreateKB method")]

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compile e execute o programa. Ele enviará automaticamente a solicitação para a API de QnA Maker para criar a base de dados de conhecimento e, em seguida, sondará os resultados a cada 30 segundos. Todas as respostas são impressas na janela do console.

Depois que sua base de dados de conhecimento tiver sido criada, você poderá exibi-la em seu Portal do QnA Maker, página [Minhas bases de dados de conhecimento](https://www.qnamaker.ai/Home/MyServices).


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
