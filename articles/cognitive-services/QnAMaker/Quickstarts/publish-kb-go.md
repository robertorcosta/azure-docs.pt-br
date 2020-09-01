---
title: 'Início rápido: publicar a base de dados de conhecimento, REST, ir QnA Maker'
description: Este início rápido baseado na REST Go publica a base de dados de conhecimento e cria um ponto de extremidade que pode ser chamado no aplicativo ou no chatbot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: b94b09fcb3bfff2eeacabaaa49eb5e4c751ec79d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267744"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Início Rápido: publicar uma base de dados de conhecimento no QnA Maker usando Go

Esse início rápido baseado em REST orienta você durante a publicação programática de sua KB (base de dados de conhecimento). A publicação envia por push a versão mais recente da base de dados de conhecimento para um índice dedicado da Azure Cognitive Search e cria um ponto de extremidade que pode ser chamado no aplicativo ou no chatbot.

Este início rápido chama as APIs de QnA Maker:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – esta API não requer nenhuma informação no corpo da solicitação.

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **Início Rápido** para o recurso no portal do Azure.

* A ID da KB (base de dados de conhecimento) do QnA Maker encontrada na URL no parâmetro de cadeia de caracteres de consulta `kbid`, conforme mostrada abaixo.

    ![ID da base de dados de conhecimento do QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se você não tiver uma base de dados de conhecimento, pode criar uma de amostra para usar para este início rápido: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE]
> Os arquivos de solução completos estão disponíveis no repositório do GitHub [ **Azure-Samples/cognitiva-Services-qnamaker-go** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Criar um arquivo Go

Abra o VSCode e crie um novo arquivo denominado `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Na parte superior de `publish-kb.go`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="dependencies":::

## <a name="create-the-main-function"></a>Criar a função principal

Após as dependências exigidas, adicione a classe a seguir:

```Go
package main

func main() {

}
```

## <a name="add-post-request-to-publish-kb"></a>Adicionar solicitação POST para publicar base de dados de conhecimento

Adicione o código a seguir, que faz uma solicitação HTTPS ao API de QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="main":::

A chamada à API retorna um status 204 de publicação bem-sucedida sem nenhum conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, essa resposta é retornada inalterada.

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Insira o comando a seguir para compilar o arquivo. O prompt de comando não retorna nenhuma informação para um build bem-sucedido.

```bash
go build publish-kb.go
```

Insira o comando a seguir em uma linha de comando para executar o programa. Ele envia a solicitação à API do QnA Maker para publicar a base de dados de conhecimento, depois imprime 204 em caso de êxito ou erros.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Após a publicação da base de dados de conhecimento, você precisa da [URL do ponto de extremidade para gerar uma resposta](./get-answer-from-knowledge-base-go.md).

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
