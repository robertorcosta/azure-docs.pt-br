---
title: 'Início Rápido: Publicar uma base de conhecimento, REST, Java – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este início rápido com base em REST Java orienta você na publicação de sua base de dados conhecimento, que envia por push a última versão da base de dados de conhecimento testada para um índice do Azure Search dedicado que representa a base de dados de conhecimento publicada. Isso também cria um ponto de extremidade que pode ser chamado no aplicativo ou chat bot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 5b97081cad352a6f6dcbf92b879584e3efa8f4ff
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802867"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Início Rápido: Publicar uma base de dados de conhecimento no QnA Maker usando Java

Esse início rápido baseado em REST orienta você durante a publicação programática de sua KB (base de dados de conhecimento). A publicação envia por push a versão mais recente da base de dados de conhecimento para um índice dedicado do Azure Search e cria um ponto de extremidade que pode ser chamado em seu aplicativo ou chat bot.

Este início rápido chama as APIs de QnA Maker:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – esta API não requer nenhuma informação no corpo da solicitação.

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* Esse exemplo usa o [cliente HTTP](https://hc.apache.org/httpcomponents-client-ga/) do Apache de componentes HTTP. Você precisa adicionar as bibliotecas de cliente Apache HTTP a seguir ao seu projeto: 
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **Início Rápido** para o recurso no portal do Azure.
* A ID da KB (base de dados de conhecimento) do QnA Maker encontrada na URL no parâmetro de cadeia de caracteres de consulta kbid, conforme mostrada abaixo.

    ![ID da base de dados de conhecimento do QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se ainda não tiver uma base de dados de conhecimento, você poderá criar uma de exemplo para usar neste início rápido: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE] 
> Os arquivos da solução completa estão disponíveis no repositório do GitHub [**Azure-Samples/cognitive-services-qnamaker-java**](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Criar um arquivo Java

Abra o VSCode e crie um novo arquivo denominado `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Na parte superior do `PublishKB.java`, acima da classe, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Criar classe PublishKB com o método principal

Após as dependências, adicione a classe a seguir:

```Go
public class PublishKB {

    public static void main(String[] args) 
    {
    }
}
```

## <a name="add-required-constants"></a>Adicionar constantes necessárias

No método **principal**, adicione as constantes necessárias para acessar o QnA Maker. Substitua os valores pelos seus próprios.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adicionar a solicitação POST para publicar a base de dados de conhecimento

Após as constantes necessárias, adicione o código a seguir, o qual faz uma solicitação HTTPS à API de QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

A chamada à API retorna um status 204 de publicação bem-sucedida sem nenhum conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, essa resposta é retornada inalterada.

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compilar e executar o programa da linha de comando. Ela enviará automaticamente a solicitação para a API de QnA Maker, depois a resposta será impressa na janela do console.

1. Compile o arquivo:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Execute o arquivo:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Próximas etapas

Após a publicação da base de dados de conhecimento, você precisa da [URL do ponto de extremidade para gerar uma resposta](../Tutorials/create-publish-answer.md#generating-an-answer).  

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
