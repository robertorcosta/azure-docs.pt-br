---
title: 'Início Rápido: Publicar base de dados de conhecimento, REST, C# – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este início rápido com base em REST em C# orienta você na publicação de sua base de dados conhecimento, que envia por push a última versão da base de dados de conhecimento testada para um índice do Azure Search dedicado que representa a base de dados de conhecimento publicada. Isso também cria um ponto de extremidade que pode ser chamado no aplicativo ou chat bot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 2b2c2ed43a229d929353767b229f8331b49a0e46
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802912"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Início Rápido: Publicar uma base de dados de conhecimento no QnA Maker usando C#

Esse início rápido baseado em REST orienta você durante a publicação programática de sua KB (base de dados de conhecimento). A publicação envia por push a versão mais recente da base de dados de conhecimento para um índice dedicado do Azure Search e cria um ponto de extremidade que pode ser chamado em seu aplicativo ou chat bot.

Este início rápido chama as APIs de QnA Maker:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – esta API não requer nenhuma informação no corpo da solicitação.

## <a name="prerequisites"></a>Pré-requisitos

* [**Edição mais recente do Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **Início Rápido** para o recurso no portal do Azure.
* A ID da KB (base de dados de conhecimento) do QnA Maker encontrada na URL no parâmetro de cadeia de caracteres de consulta kbid, conforme mostrada abaixo.

    ![ID da base de dados de conhecimento do QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se ainda não tiver uma base de dados de conhecimento, você poderá criar uma de exemplo para usar neste início rápido: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE] 
> Os arquivos da solução completa estão disponíveis no repositório do GitHub [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Criar projeto da base de dados de conhecimento

1. Abra o Visual Studio 2019 Community Edition.
1. Crie um novo projeto de **Aplicativo do Console (.NET Core)** e nomeie o projeto `QnaMakerQuickstart`. Aceite os padrões para as configurações restantes.

## <a name="add-required-dependencies"></a>Adicionar as dependências necessárias

No topo do Program.cs, substitua a única instrução using pelas linhas abaixo para adicionar as dependências necessárias ao projeto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Adicionar constantes necessárias

Na classe **Programa**, adicione as constantes necessárias para acessar o QnA Maker.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Adicionar o método Principal para publicar a base de dados de conhecimento

Após as constantes necessárias, adicione o código a seguir, o qual faz uma solicitação HTTPS à API de QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=36-56 "Add HTTP Post request and response")]

A chamada à API retorna um status 204 de publicação bem-sucedida sem nenhum conteúdo no corpo da resposta. 
 
## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compile e execute o programa. Ela enviará automaticamente a solicitação para a API de QnA Maker para publicar a base de dados de conhecimento e, em seguida, a resposta será impressa na janela do console.

Após sua base de dados de conhecimento ser publicada, você poderá consultá-la do ponto de extremidade com um aplicativo cliente ou chat bot. 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Próximas etapas

Após a publicação da base de dados de conhecimento, você precisa da [URL do ponto de extremidade para gerar uma resposta](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
